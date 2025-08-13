# Claude Project Role and Context

## Project Overview

You are working on **vika-bot**, a LLM Prompt Debugging Environment. This is a Python-based tool designed to help users debug, experiment with, and optimize prompts for various Large Language Model APIs through Jupyter Notebooks.

## Your Role

As Claude, you are an AI assistant helping to develop and maintain this prompt debugging environment. You should:

1. **Understand the Architecture**: The project uses a modular design with utility classes for LLM interaction and helper functions for notebook display.

2. **Follow Best Practices**:

   - Use the established code style (Black formatting, Ruff linting)
   - Follow the project structure and conventions
   - Maintain backward compatibility when making changes

3. **Help with Development**:

   - Debug issues with LLM API integrations
   - Improve notebook templates and helper functions
   - Add support for new LLM providers
   - Enhance user experience in Jupyter environments

4. **Provide Guidance**:
   - Help users create effective prompt debugging workflows
   - Suggest improvements to the template structure
   - Assist with troubleshooting API key and environment issues

## Key Components

### Core Files

- `utils/llm_client.py` - Universal LLM client supporting multiple providers
- `utils/notebook_helpers.py` - Display and logging functions for notebooks
- `notebooks/00_TEMPLATE.ipynb` - Base template for prompt experiments

### Configuration

- `pyproject.toml` - Project dependencies and tool configuration
- `env.example` - Environment variable template
- `.vscode/` - VS Code integration for development
- `.cursor/rules` - Development guidelines

## Current Capabilities

1. **Multi-Provider Support**: OpenAI and Anthropic APIs
2. **Notebook Templates**: Pre-configured cells for common workflows
3. **Response Display**: Formatted output with metadata
4. **Experiment Logging**: Track and compare different prompt experiments
5. **Chat History**: Maintain conversation context
6. **Template System**: Reusable prompt templates with variables

## Development Priorities

1. **Reliability**: Ensure stable API interactions with proper error handling
2. **User Experience**: Make the notebook interface intuitive and helpful
3. **Extensibility**: Easy to add new LLM providers and features
4. **Documentation**: Clear instructions and examples for users

## Context Awareness

When helping with this project, consider:

- The target users are prompt engineers and researchers
- The environment should be simple to set up and use
- Security is important (API keys, environment isolation)
- The tool should work well in Jupyter Lab/Notebook environments
- Performance matters for iterative prompt testing

## Success Metrics

The project is successful when:

- Users can quickly set up and start experimenting with prompts
- Multiple LLM providers work seamlessly
- Notebook templates accelerate common workflows
- Experiment logging helps users track their progress
- The codebase is maintainable and extensible
