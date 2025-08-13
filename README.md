# OpenAI Prompt Debugging Environment

Simple environment for experimenting with OpenAI API through Jupyter Notebooks.

## Features

- 🚀 **Direct OpenAI Integration** - Pure OpenAI API without abstractions
- 📝 **Simple Template** - Ready-to-use notebook template
- 🔍 **Usage Statistics** - See token usage and costs
- 💰 **Real-time Cost Tracking** - USD cost calculation for 400+ LLM models
- 🛠️ **Developer Friendly** - VS Code integration and clean setup

## Quick Start

1. **Clone and setup**:

   ```bash
   git clone <repository-url>
   cd vika-bot
   ```

2. **Install dependencies**:

   ```bash
   uv sync
   ```

3. **Configure API keys**:

   ```bash
   cp env.example .env
   # Edit .env file and add your API keys
   ```

4. **Start Jupyter Lab**:

   ```bash
   uv run jupyter lab
   ```

5. **Create your first experiment**:
   ```bash
   cp notebooks/00_TEMPLATE.ipynb notebooks/my_experiment.ipynb
   ```

## Environment Variables

Create a `.env` file in the project root:

```bash
# OpenAI Configuration
OPENAI_API_KEY=your_openai_api_key_here
```

## Project Structure

```
vika-bot/
├── .env.example              # Environment variables template
├── .gitignore               # Git exclusions
├── pyproject.toml           # uv dependencies
├── README.md                # This file
├── CLAUDE.md                # Claude AI context
├── notebooks/
│   ├── 00_TEMPLATE.ipynb    # Base notebook template
│   └── example_openai.ipynb # Usage example
├── utils/
│   ├── __init__.py
│   ├── llm_client.py        # Universal LLM client
│   └── notebook_helpers.py  # Display and logging helpers
├── .cursor/
│   └── rules                # Cursor development rules
└── .vscode/
    ├── tasks.json          # VS Code tasks
    └── settings.json       # VS Code settings
```

## Usage

### Basic Usage

```python
from openai import OpenAI
from dotenv import load_dotenv
from tokencost import calculate_prompt_cost, calculate_completion_cost

# Load environment variables
load_dotenv()

# Create client
client = OpenAI()

# Make request
response = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[
        {"role": "user", "content": "Explain quantum computing in simple terms"}
    ],
    temperature=0.7
)

# Get response and calculate costs
answer = response.choices[0].message.content
messages = [{"role": "user", "content": "Explain quantum computing in simple terms"}]

prompt_cost = calculate_prompt_cost(messages, "gpt-4o-mini")
completion_cost = calculate_completion_cost(answer, "gpt-4o-mini")
total_cost = prompt_cost + completion_cost

print(f"Response: {answer}")
print(f"Total cost: ${total_cost:.6f} USD")
```

### Template-Based Workflow

1. Copy the template notebook:

   ```bash
   cp notebooks/00_TEMPLATE.ipynb notebooks/my_experiment.ipynb
   ```

2. Open in Jupyter Lab and follow the template structure:
   - **Configuration**: Set provider, model, and parameters
   - **Prompt Writing**: Create system and user prompts
   - **Execution**: Run the LLM request
   - **Analysis**: Review response and metadata
   - **Cost Tracking**: See real-time USD costs for requests
   - **Logging**: Save experiments for comparison

### Supported Providers

#### OpenAI

- Models: `gpt-4o`, `gpt-4o-mini`, `gpt-3.5-turbo`
- Requires: `OPENAI_API_KEY`

#### Anthropic

- Models: `claude-3-sonnet-20240229`, `claude-3-haiku-20240307`
- Requires: `ANTHROPIC_API_KEY`

## Development

### VS Code Integration

The project includes VS Code tasks for common operations:

- **Ctrl+Shift+P** → "Tasks: Run Task"
  - "Start Jupyter Lab" - Launch the notebook environment
  - "Install Dependencies" - Run `uv sync`
  - "Format Code" - Run Black formatter
  - "Lint Code" - Run Ruff linter
  - "Create New Notebook" - Copy template with custom name

### Code Style

- **Formatter**: Black (line length: 88)
- **Linter**: Ruff
- **Type Hints**: Encouraged
- **Docstrings**: Required for public functions

### Adding New Providers

1. Extend `LLMClient._initialize_client()` method
2. Add provider-specific completion method
3. Update configuration examples
4. Test with the template notebook

## Troubleshooting

### Common Issues

**API Key Errors**:

- Ensure `.env` file exists and contains valid API keys
- Check that environment variables are loaded (`load_dotenv()`)

**Import Errors**:

- Run `uv sync` to install dependencies
- Ensure you're in the correct virtual environment

**Jupyter Issues**:

- Try restarting the kernel
- Check that `ipykernel` is installed
- Verify notebook file permissions

### Getting Help

1. Check the example notebook for working configurations
2. Review the template notebook structure
3. Examine error messages for specific API issues
4. Ensure all dependencies are installed with `uv sync`

## License

This project is open source. See LICENSE file for details.

## Contributing

1. Fork the repository
2. Create a feature branch
3. Follow the code style guidelines
4. Add tests for new functionality
5. Submit a pull request

---

**Note**: This project requires Python 3.13+ and valid API keys for the LLM providers you plan to use.
