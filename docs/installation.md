# Installation

## Requirements

- Python 3.11 or higher
- An OpenRouter API key (free tier available at https://openrouter.ai/keys)

## Install from PyPI (Recommended)

```bash
pip install kurocode-cli
```

This installs the latest stable release with all dependencies.

## Install from Source

```bash
# Clone the repository
git clone <repo-url>
cd KuroCode

# Create virtual environment (recommended)
python -m venv .venv
source .venv/bin/activate  # Linux/macOS
.venv\Scripts\activate     # Windows

# Install in development mode
pip install -e .

# Or with development dependencies
pip install -e ".[dev]"
```

## Using uv (Recommended for Contributors)

```bash
# Clone and install with uv
git clone <repo-url>
cd KuroCode
uv sync --all-extras
```

## Verify Installation

```bash
# Check CLI is available
kurocode --help

# Verify configuration
kurocode config list
```

## System Dependencies

KuroCode uses these core packages (installed automatically):

| Package | Purpose |
|---------|---------|
| `click` | CLI framework |
| `httpx` | Async HTTP client |
| `pydantic` | Settings validation |
| `rich` | Terminal formatting |
| `aiosqlite` | Conversation storage |
| `prompt-toolkit` | Interactive REPL |

## Environment Variables

Optionally set environment variables in `.env`:

```bash
# Required
KUROCODE_API_KEY=sk-or-v1-...

# Optional
KUROCODE_BASE_URL=https://openrouter.ai/api/v1
KUROCODE_TIMEOUT=60.0
KUROCODE_DB_PATH=~/.local/share/kurocode/history.db
```

## Troubleshooting

### "Command not found"

Ensure your Python scripts directory is in your PATH:

```bash
# Check pip install location
pip show kurocode-cli

# Add to PATH (Linux/macOS)
export PATH="$PATH:$(python -m site --user-base)/bin"

# Windows
# Scripts directory is typically in PATH automatically
```

### Import Errors

Ensure you're using Python 3.11+:

```bash
python --version
```
