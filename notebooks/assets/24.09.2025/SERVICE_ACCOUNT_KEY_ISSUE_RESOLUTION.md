# Решение проблемы с созданием ключей сервисного аккаунта

Вы столкнулись с политикой организации, которая блокирует создание ключей сервисных аккаунтов из соображений безопасности. Это обычная практика в современных организациях Google Cloud. Рассмотрим несколько способов решения этой проблемы.

## Способ 1: Использование Application Default Credentials (ADC) для локальной разработки

### Что такое ADC

Application Default Credentials (ADC) — это безопасная альтернатива ключам сервисных аккаунтов для локальной разработки. ADC автоматически находит учетные данные в зависимости от среды выполнения.

### Настройка ADC

**Шаг 1: Аутентификация через gcloud CLI**

Выполните следующую команду для аутентификации:

```bash
gcloud auth application-default login
```

Эта команда:
- Откроет браузер для входа в ваш Google аккаунт.
- Сохранит учетные данные в `~/.config/gcloud/application_default_credentials.json` (Linux/macOS).
- Предоставит разрешения для работы с Google Cloud APIs.

**Шаг 2: Предоставление прав вашему аккаунту**

В Google Cloud Console перейдите в **IAM & Admin** → **IAM** и добавьте вашему Google аккаунту необходимые роли:
1. **Storage Object Admin** - для работы с Cloud Storage.
2. При необходимости другие роли для вашего проекта.

### Модификация Go кода для работы с ADC

Измените ваш код для использования ADC вместо JSON ключа:

```go
package main

import (
    "context"
    "cloud.google.com/go/storage"
    "google.golang.org/api/option"
)

func createStorageClient() (*storage.Client, error) {
    ctx := context.Background()
    // Используем Application Default Credentials
    // Убираем option.WithCredentialsFile()
    client, err := storage.NewClient(ctx)
    if err != nil {
        return nil, err
    }
    return client, nil
}
```

**Важно:** Убедитесь, что переменная окружения `GOOGLE_APPLICATION_CREDENTIALS` НЕ установлена, чтобы Go SDK использовал ADC.

## Способ 2: Настройка Workload Identity Federation

Если вам нужна автоматизация (CI/CD), используйте Workload Identity Federation.

### Создание Workload Identity Pool

```bash
gcloud iam workload-identity-pools create "my-pool" --project="your-project-id" --location="global" --display-name="Pool for local development"
```

### Создание провайдера

```bash
gcloud iam workload-identity-pools providers create-oidc "my-provider" --project="your-project-id" --location="global" --workload-identity-pool="my-pool" --display-name="My OIDC provider" --attribute-mapping="google.subject=assertion.sub"
```

## Способ 3: Запрос на изменение организационной политики

Если вам действительно нужны ключи сервисных аккаунтов, обратитесь к администратору организации.

### Роли, необходимые администратору

Администратор должен иметь роль **Organization Policy Administrator** (`roles/orgpolicy.policyAdmin`).

### Отключение политики для конкретного проекта

Администратор может создать исключение для вашего проекта:
1. Перейти в **IAM & Admin** → **Organization Policies**.
2. Найти политику `iam.disableServiceAccountKeyCreation`.
3. Нажать **Edit policy**.
4. Добавить исключение для вашего проекта.

## Способ 4: Использование compute instance с attached service account

Если ваше приложение запущено на GCP Compute Engine или Cloud Run, используйте прикрепленный сервисный аккаунт.

### Настройка на Compute Engine

```go
func createStorageClientOnGCE() (*storage.Client, error) {
    ctx := context.Background()
    // На Compute Engine автоматически используется attached service account
    client, err := storage.NewClient(ctx)
    if err != nil {
        return nil, err
    }
    return client, nil
}
```

### Получение токена через metadata server

Для отладки можете проверить метаданные инстанса:

```bash
curl -H "Metadata-Flavor: Google" "http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/token"
```

## Рекомендуемый подход для вашего случая

**Для локальной разработки:**
1. Используйте `gcloud auth application-default login`.
2. Предоставьте вашему Google аккаунту роль **Storage Object Admin**.
3. Уберите из кода `option.WithCredentialsFile()`.

**Для продакшена:**
1. Если на GCP - используйте attached service account.
2. Если вне GCP - настройте Workload Identity Federation.
3. В крайнем случае - попросите администратора создать исключение для политики.

### Преимущества ADC:
- ✅ Нет необходимости управлять JSON ключами.
- ✅ Автоматическое обновление токенов.
- ✅ Более безопасно.
- ✅ Поддерживается всеми Google Cloud SDK.

### Измененный код для работы с ADC:

```go
package main

import (
    "context"
    "fmt"
    "io"
    "mime/multipart"
    "net/http"
    "time"
    "cloud.google.com/go/storage"
    "github.com/gin-gonic/gin"
)

func createStorageClient() (*storage.Client, error) {
    ctx := context.Background()
    // ADC автоматически найдет учетные данные
    client, err := storage.NewClient(ctx)
    if err != nil {
        return nil, fmt.Errorf("failed to create storage client: %v", err)
    }
    return client, nil
}

func uploadAvatar(client *storage.Client, bucketName string, objectName string, file multipart.File) (string, error) {
    ctx := context.Background()
    wc := client.Bucket(bucketName).Object(objectName).NewWriter(ctx)
    wc.ACL = []storage.ACLRule{{Entity: storage.AllUsers, Role: storage.RoleReader}}
    wc.ContentType = "image/jpeg"
    
    if _, err := io.Copy(wc, file); err != nil {
        return "", err
    }
    if err := wc.Close(); err != nil {
        return "", err
    }
    return fmt.Sprintf("https://storage.googleapis.com/%s/%s", bucketName, objectName), nil
}

func main() {
    router := gin.Default()
    router.MaxMultipartMemory = 8 << 20 // 8 MB
    router.POST("/users/:userID/avatar", func(c *gin.Context) {
        file, header, err := c.Request.FormFile("avatar")
        if err != nil {
            c.JSON(http.StatusBadRequest, gin.H{"error": "Файл не найден"})
            return
        }
        defer file.Close()

        client, err := createStorageClient()
        if err != nil {
            c.JSON(http.StatusInternalServerError, gin.H{"error": "Ошибка подключения к Storage"})
            return
        }
        defer client.Close()

        userID := c.Param("userID")
        objectName := fmt.Sprintf("avatars/%s_%d_%s", userID, time.Now().Unix(), header.Filename)
        avatarURL, err := uploadAvatar(client, "your-bucket-name", objectName, file)
        if err != nil {
            c.JSON(http.StatusInternalServerError, gin.H{"error": "Ошибка загрузки"})
            return
        }
        c.JSON(http.StatusOK, gin.H{
            "message":   "Аватарка успешно загружена",
            "avatar_url": avatarURL,
        })
    })
    router.Run(":8080")
}
```