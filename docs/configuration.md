# Configuration

KuroCode uses a layered configuration system with multiple sources.

## Configuration Sources (Priority Order)

1. **Environment Variables** (highest priority)
2. **`.env` File**
3. **TOML Config File** (default or profile-specific)
4. **Pydantic Field Defaults** (lowest priority)

## Setting Up Your API Key

### Option 1: Config Command

```bash
kurocode config set api_key "sk-or-v1-..."
```

This saves to `~/.config/kurocode/config.toml`.

### Option 2: Environment Variable

```bash
export KUROCODE_API_KEY="sk-or-v1-..."
```

### Option 3: .env File

Create `.env` in your project directory:

```env
KUROCODE_API_KEY=sk-or-v1-...
```

### Option 4: TOML Config File

Edit `~/.config/kurocode/config.toml`:

```toml
[default]
api_key = "sk-or-v1-..."
```

## View Current Configuration

```bash
kurocode config list
```

Output:

```
api_key = ***
base_url = https://openrouter.ai/api/v1
timeout = 60.0
site_url = 
app_name = kurocode
max_retries = 3
db_path = /home/user/.local/share/kurocode/history.db
```

## Configuration Profiles

Profiles allow multiple configurations (e.g., work vs personal).

### Create a Profile

```bash
kurocode config set api_key "sk-or-v1-work..." --profile work
```

### Use a Profile

```bash
kurocode --profile work ask "Hello"
```

### TOML Profile Structure

```toml
[default]
api_key = "sk-or-v1-personal..."

[profiles.work]
api_key = "sk-or-v1-work..."
site_url = "https://work.example.com"

[profiles.personal]
api_key = "sk-or-v1-personal..."
```

## All Settings

| Setting | Type | Default | Description |
|---------|------|---------|-------------|
| `api_key` | string | **required** | OpenRouter API key |
| `base_url` | string | `https://openrouter.ai/api/v1` | API base URL |
| `timeout` | float | `60.0` | HTTP timeout (seconds) |
| `site_url` | string | `""` | HTTP-Referer header |
| `app_name` | string | `kurocode` | X-Title header |
| `max_retries` | int | `3` | Retry attempts for 429/5xx |
| `db_path` | path | `~/.local/share/kurocode/history.db` | SQLite database path |

## Environment Variable Mapping

Environment variables use the `KUROCODE_` prefix:

```bash
KUROCODE_API_KEY=...
KUROCODE_BASE_URL=...
KUROCODE_TIMEOUT=...
KUROCODE_SITE_URL=...
KUROCODE_APP_NAME=...
KUROCODE_MAX_RETRIES=...
KUROCODE_DB_PATH=...
```

## Config File Locations

| Platform | Default Path |
|----------|--------------|
| Linux | `~/.config/kurocode/config.toml` |
| macOS | `~/Library/Application Support/kurocode/config.toml` |
| Windows | `%APPDATA%\kurocode\config.toml` |

Override with `KUROCODE_CONFIG` environment variable.

## Validation

Configuration is validated on startup using Pydantic. Invalid values will raise `ConfigError` with helpful hints:

```
✗ Error: Configuration validation failed.
  Hint: 1 validation error for Settings
  api_key
    Field required
```
