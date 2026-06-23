# KuroCode CLI

KuroCode is a lightweight terminal client for accessing OpenRouter's free AI models directly from your command line.

## Features

- **One-Shot Queries**: Quick questions with pipe-friendly JSON output
- **Interactive Chat**: REPL with slash commands and session persistence
- **Model Discovery**: List, search, and get info on free models
- **Conversation History**: SQLite-backed history with export to markdown
- **Multiple Output Formats**: Rich (colored), plain text, or JSON

## Quick Start

```bash
# Install
pip install -e .

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
| [Installation](installation.md) | Setup and installation guide |
| [Configuration](configuration.md) | API keys, profiles, and settings |
| [Commands](commands/ask.md) | CLI command reference |
| [Development](development.md) | Contributing and development setup |
| [Architecture](architecture.md) | Code structure and design decisions |
