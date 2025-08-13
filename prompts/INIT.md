# LLM Prompt Debugging Environment Implementation Plan

## Goal

Create a simple and intuitive environment for debugging prompts and working with various LLM APIs through Jupyter Notebooks.

## Core Requirements

- Template-based notebook creation system
- Support for OpenAI and other LLM providers
- Local deployment without Docker
- Secure API key storage in `.env`
- Using `uv` as package manager

## Step-by-Step Plan

### Stage 1: Basic Project Setup

1. **Set up project structure**

   - Create `.gitignore` (Python, Jupyter, `.env`)
   - Create `pyproject.toml` with dependencies for `uv`
   - Create `.env.example` with variable examples

2. **Configure dependencies**
   - Core: `jupyter`, `ipykernel`
   - LLM APIs: `openai`, `anthropic`
   - Utils: `python-dotenv`, `rich`
   - Dev: `ruff`, `black` (minimal)

### Stage 2: Template System Creation

3. **Create basic notebook template**

   - `notebooks/00_TEMPLATE.ipynb`
   - Cells: settings, prompt, request, output
   - Simple functions for API interaction

4. **Create utilities**
   - `utils/llm_client.py` - simple client for different providers
   - `utils/notebook_helpers.py` - functions for markdown rendering

### Stage 3: Development Environment Setup

5. **VS Code integration**

   - `.vscode/tasks.json` - tasks for running Jupyter, formatting
   - `.vscode/settings.json` - basic settings

6. **Cursor/Claude integration**
   - `.cursor/rules` - simple assistant rules
   - `CLAUDE.md` - project role and context

### Stage 4: Documentation and Examples

7. **Create documentation**

   - `README.md` - quick start and instructions
   - Usage examples

8. **Create example notebook**
   - `notebooks/example_openai.ipynb` - working example

## Detailed Project Structure

```
vika-bot/
├── .env.example                 # Environment variables examples
├── .gitignore                   # Git exclusions
├── pyproject.toml               # uv dependencies
├── README.md                    # Documentation
├── CLAUDE.md                    # Claude role definition
├── notebooks/
│   ├── 00_TEMPLATE.ipynb        # Notebook template
│   └── example_openai.ipynb     # Usage example
├── utils/
│   ├── __init__.py
│   ├── llm_client.py           # LLM APIs client
│   └── notebook_helpers.py      # Helper functions
├── .cursor/
│   └── rules                    # Cursor rules
└── .vscode/
    ├── tasks.json              # VS Code tasks
    └── settings.json           # VS Code settings
```

## Key Environment Variables

```bash
# .env
OPENAI_API_KEY=your_openai_key
ANTHROPIC_API_KEY=your_anthropic_key
DEFAULT_LLM_PROVIDER=openai
DEFAULT_MODEL=gpt-4o-mini
```

## Main Workflow

1. **Create new notebook:**

   ```bash
   cp notebooks/00_TEMPLATE.ipynb notebooks/my_experiment.ipynb
   ```

2. **Launch environment:**

   ```bash
   uv sync
   uv run jupyter lab
   ```

3. **Work in notebook:**
   - Configure model and parameters
   - Write prompt
   - Execute request
   - Get formatted response

## Success Criteria

- ✅ Environment launches with one command
- ✅ New notebook created by copying template
- ✅ Support for minimum 2 LLM providers
- ✅ All secrets in `.env` and excluded from git
- ✅ LLM responses displayed as markdown in notebook
- ✅ Easy provider switching via environment variables

## Implementation Principles

- **Simplicity**: minimum dependencies, maximum functionality
- **Practicality**: solves real prompt debugging tasks
- **Security**: all keys in `.env`, nothing gets into git
- **Extensibility**: easy to add new providers
- **Documentation**: clear instructions and examples

This plan focuses on creating a working environment without unnecessary complexity that can be easily used and extended.
