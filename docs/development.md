# Development Guide

This guide covers setting up a development environment and contributing to KuroCode.

## Getting Started

### Prerequisites

- Python 3.11+
- uv (recommended) or pip
- Git

### Clone and Setup

```bash
git clone <repo-url>
cd KuroCode

# With uv (recommended)
uv sync --all-extras

# Or with pip
python -m venv .venv
source .venv/bin/activate
pip install -e ".[dev]"
```

## Project Structure

```
kurocode-cli/
├── src/kurocode/
│   ├── __main__.py          # CLI entry point
│   ├── types.py             # Shared types
│   ├── exceptions.py        # Exception hierarchy
│   ├── commands/            # CLI commands
│   ├── core/                # Business logic
│   ├── infra/               # Infrastructure (API, DB, config)
│   └── tests/               # Test suite
├── tests/                   # Root test config
├── docs/                    # Documentation
├── pyproject.toml           # Project configuration
└── .github/workflows/       # CI/CD
```

## Development Commands

### Linting

```bash
# Check for issues
ruff check src/ tests/

# Auto-fix
ruff check --fix src/ tests/

# Format code
ruff format src/ tests/
```

### Type Checking

```bash
mypy src/
```

### Testing

```bash
# Run all tests
pytest

# Run with coverage
pytest --cov=kurocode --cov-report=term-missing

# Run specific test
pytest src/kurocode/tests/unit/test_session.py

# Run tests matching pattern
pytest -k "test_add_message"
```

## Code Style

### Ruff Rules

Enabled rules in `pyproject.toml`:

| Rule | Description |
|------|-------------|
| E, W | Pycodestyle errors/warnings |
| F | Pyflakes |
| I | isort |
| UP | pyupgrade |
| B | flake8-bugbear |
| C4 | flake8-comprehensions |
| SIM | flake8-simplify |
| ANN | flake8-annotations |
| RUF | Ruff-specific rules |

### Type Annotations

All code must be strictly typed. Run `mypy src/` to verify.

### Docstrings

Use Google-style docstrings for public APIs:

```python
def fetch_models(force_refresh: bool = False) -> list[ModelInfo]:
    """
    Return the list of free OpenRouter models.

    Parameters
    ----------
    force_refresh:
        When True, bypass cache and re-fetch from network.

    Returns
    -------
    list[ModelInfo]
        List of available free models.
    """
```

## Testing

### Unit Tests

Pure tests with no I/O:

```python
def test_add_user_message() -> None:
    session = Session(model_id="test")
    msg = session.add_user_message("Hello")
    assert msg.role == "user"
    assert msg.content == "Hello"
```

### CLI Tests

Using Click's `CliRunner`:

```python
from click.testing import CliRunner
from kurocode.__main__ import cli

def test_ask_help() -> None:
    runner = CliRunner()
    result = runner.invoke(cli, ["ask", "--help"])
    assert result.exit_code == 0
```

### Integration Tests

Tests with mocked HTTP:

```python
import httpx
import respx

@respx.mock
async def test_fetch_models() -> None:
    respx.get("https://openrouter.ai/api/v1/models").mock(
        return_value=httpx.Response(200, json={"data": [...]})
    )
    registry = ModelRegistry()
    models = await registry.fetch()
    assert len(models) > 0
```

## CI/CD

GitHub Actions runs on every push/PR:

```yaml
steps:
  - uses: astral-sh/setup-uv@v3
  - run: uv sync --all-extras
  - run: uv run ruff check src/ tests/
  - run: uv run mypy src/
  - run: uv run pytest --tb=short
```

Ensure all checks pass before pushing.

## Adding a New Command

1. Create `src/kurocode/commands/mycommand.py`:

```python
import click
from kurocode.types import CliContext

@click.command()
@click.pass_obj
def mycommand(ctx: CliContext) -> None:
    """My new command."""
    ctx.renderer.info("Hello from mycommand!")
```

2. Register in `src/kurocode/__main__.py`:

```python
from kurocode.commands.mycommand import mycommand
cli.add_command(mycommand, "mycommand")
```

3. Add tests in `src/kurocode/tests/cli/test_mycommand.py`

## Adding a New Configuration Field

1. Add field to `Settings` in `src/kurocode/infra/config.py`:

```python
my_setting: str = Field(
    default="default",
    description="My new setting.",
)
```

2. The field is automatically:
   - Read from `KUROCODE_MY_SETTING` env var
   - Read from TOML `[default]` section
   - Validated by Pydantic
   - Shown in `config list`

## Commit Messages

Follow conventional commits:

- `feat:` new feature
- `fix:` bug fix
- `docs:` documentation
- `refactor:` code restructuring
- `test:` adding tests
- `chore:` maintenance

Examples:

```
feat: add /export slash command to chat
fix: handle rate limit errors gracefully
docs: update installation guide
```

## Release Process

1. Update version in `pyproject.toml`
2. Update `CHANGELOG.md`
3. Create git tag: `git tag v0.1.0`
4. Push: `git push origin main --tags`
