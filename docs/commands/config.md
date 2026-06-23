# config Command

The `config` command manages KuroCode configuration stored in TOML format.

## Usage

```bash
kurocode config <subcommand>
```

## Subcommands

| Subcommand | Description |
|------------|-------------|
| `set <key> <value>` | Set a configuration value |
| `list` | List current resolved configuration |

## Examples

### Set Configuration Value

```bash
# Set API key in default section
kurocode config set api_key "sk-or-v1-..."

# Set with profile
kurocode config set api_key "sk-or-v1-work..." --profile work

# Set other values
kurocode config set timeout 30
kurocode config set max_retries 5
kurocode config set base_url "https://custom-api.example.com"
```

### List Configuration

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

### List Profile Configuration

```bash
kurocode config list --profile work
```

## Configuration File

Default location: `~/.config/kurocode/config.toml`

### File Structure

```toml
# Default settings (applied to all profiles)
api_key = "sk-or-v1-default..."
timeout = 60.0

# Profile-specific overrides
[profiles.work]
api_key = "sk-or-v1-work..."
site_url = "https://work.example.com"

[profiles.personal]
api_key = "sk-or-v1-personal..."
```

### Creating Profiles

```bash
# Create a work profile
kurocode config set api_key "sk-or-v1-work..." --profile work
kurocode config set site_url "https://work.example.com" --profile work

# Create a personal profile
kurocode config set api_key "sk-or-v1-personal..." --profile personal
```

### Using Profiles

```bash
# Use work profile
kurocode --profile work ask "Hello"

# Use personal profile
kurocode --profile personal chat
```

## Value Types

Values are automatically type-casted:

| Input | Parsed As |
|-------|-----------|
| `true` / `false` | Boolean |
| `123` | Integer |
| `1.5` | Float |
| `"text"` | String |

Examples:

```bash
kurocode config set timeout 30        # Integer
kurocode config set timeout 30.5      # Float
kurocode config set verbose true      # Boolean
```

## Config File Locations

| Platform | Default Path |
|----------|--------------|
| Linux | `~/.config/kurocode/config.toml` |
| macOS | `~/Library/Application Support/kurocode/config.toml` |
| Windows | `%APPDATA%\kurocode\config.toml` |

### Override Path

```bash
export KUROCODE_CONFIG="/path/to/config.toml"
```

## Environment Variables

Environment variables override TOML values:

```bash
export KUROCODE_API_KEY="sk-or-v1-..."
export KUROCODE_TIMEOUT=30
```

## Error Handling

Invalid TOML syntax raises `ConfigError`:

```
✗ Error: Failed to parse config file: /home/user/.config/kurocode/config.toml
  Hint: ...parse error details...
```

Missing required fields raise validation errors:

```
✗ Error: Configuration validation failed.
  Hint: 1 validation error for Settings
  api_key
    Field required
```
