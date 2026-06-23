# KuroCode

[![PyPI version](https://badge.fury.io/py/kurocode-cli.svg)](https://pypi.org/project/kurocode-cli/)
[![Python](https://img.shields.io/pypi/pyversions/kurocode-cli.svg)](https://pypi.org/project/kurocode-cli/)
[![License](https://img.shields.io/pypi/l/kurocode-cli.svg)](https://pypi.org/project/kurocode-cli/)

A lightweight terminal client for OpenRouter's free AI models. Access AI models directly from your command line with streaming responses, interactive chat, and conversation history.

## Installation

```bash
pip install kurocode-cli
```

## Quick Start

```bash
# Install
pip install kurocode-cli

# Configure API key
kurocode config set api_key "sk-or-v1-..."

# Ask a question
kurocode ask "Explain async/await in Python"

# Start interactive chat
kurocode chat
```

## Documentation

| Section | Description |
|---------|-------------|
| [Installation](docs/installation.md) | Setup and installation guide |
| [Configuration](docs/configuration.md) | API keys, profiles, and settings |
| [ask Command](docs/commands/ask.md) | One-shot queries |
| [chat Command](docs/commands/chat.md) | Interactive REPL |
| [models Command](docs/commands/models.md) | Model discovery |
| [history Command](docs/commands/history.md) | Conversation history |
| [config Command](docs/commands/config.md) | Configuration management |
| [Development](docs/development.md) | Contributing guide |
| [Architecture](docs/architecture.md) | Code structure |

## Features

- **One-Shot Queries**: Quick questions with pipe-friendly JSON output
- **Interactive Chat**: REPL with slash commands and session persistence
- **Model Discovery**: List, search, and get info on free models
- **Conversation History**: SQLite-backed history with export to markdown
- **Multiple Output Formats**: Rich (colored), plain text, or JSON

## Commands

### ask - One-Shot Queries

```bash
kurocode ask "Explain async/await in Python"
kurocode ask "What is the capital of France?" --model anthropic/claude-3-haiku
cat main.py | kurocode ask "Find the bug in this code"
kurocode --output-format json --no-stream ask "Explain async/await" | jq .content
```

### chat - Interactive REPL

```bash
kurocode chat
kurocode chat --model anthropic/claude-3-haiku
kurocode chat --resume <conversation_id>
```

**Slash Commands:**
- `/help` - Show available commands
- `/switch <model_id>` - Switch model
- `/model <model_id>` - Alias for /switch
- `/clear` - Clear session context
- `/save <filename>` - Save transcript to markdown

### models - Model Discovery

```bash
kurocode models list
kurocode models list --refresh
kurocode models search "mistral"
kurocode models info openai/gpt-4o-mini
```

### history - Conversation History

```bash
kurocode history list --limit 20
kurocode history view <conversation_id>
kurocode history export <conversation_id> --format markdown > transcript.md
```

### config - Configuration

```bash
kurocode config set api_key "sk-or-v1-..."
kurocode config set timeout 30
kurocode config list
kurocode --profile work config list
```

## Global Options

| Option | Description |
|--------|-------------|
| `--profile <name>` | Load a specific configuration profile |
| `--output-format <rich\|plain\|json>` | Change output rendering style |
| `--no-stream` | Wait for full response (useful for scripting) |

## Configuration

```bash
# Set API key
kurocode config set api_key "sk-or-v1-..."

# Or use environment variable
export KUROCODE_API_KEY="sk-or-v1-..."

# View configuration
kurocode config list
```

See [Configuration Guide](docs/configuration.md) for details on profiles, TOML config, and all settings.

## Development (Contributors)

```bash
# Clone the repository
git clone <repo-url>
cd KuroCode

# Install with dev dependencies
pip install -e ".[dev]"

# Run linter
ruff check src/ tests/

# Run type checker
mypy src/

# Run tests
pytest
```

See [Development Guide](docs/development.md) for contributing guidelines.

## License

MIT