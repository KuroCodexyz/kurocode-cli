# models Command

The `models` command discovers and lists free AI models available on OpenRouter.

## Usage

```bash
kurocode models <subcommand>
```

## Subcommands

| Subcommand | Description |
|------------|-------------|
| `list` | List all free models |
| `search <query>` | Search models by name or ID |
| `info <model_id>` | Show details for a specific model |

## Examples

### List All Free Models

```bash
kurocode models list
```

Output (Rich format):

```
┌──────────────────────────┬─────────────────────────────────┬──────────┐
│ Model ID                 │ Name                            │ Context  │
├──────────────────────────┼─────────────────────────────────┼──────────┤
│ openai/gpt-4o-mini       │ GPT-4o Mini                     │ 128,000  │
│ anthropic/claude-3-haiku │ Claude 3 Haiku                  │ 200,000  │
│ google/gemma-2-9b        │ Gemma 2 9B                      │ 8,192    │
└──────────────────────────┴─────────────────────────────────┴──────────┘
```

### Force Refresh Cache

```bash
kurocode models list --refresh
```

Models are cached locally; use `--refresh` to fetch the latest from OpenRouter.

### Search Models

```bash
# Search by name
kurocode models search "mistral"

# Search by ID
kurocode models search "claude"
```

### Get Model Info

```bash
kurocode models info openai/gpt-4o-mini
```

Output:

```
I Model ID: openai/gpt-4o-mini
I Name: GPT-4o Mini
I Context Length: 128000
I Description: GPT-4o Mini is a fast, affordable model...
```

## Output Formats

### Rich (default)

Table with colored columns for ID, name, and context length.

### Plain

Tab-separated values:

```
ID	NAME	CONTEXT
openai/gpt-4o-mini	GPT-4o Mini	128000
```

### JSON

```json
[
  {
    "id": "openai/gpt-4o-mini",
    "name": "GPT-4o Mini",
    "context_length": 128000
  }
]
```

## Model Caching

Models are cached at two levels:

1. **In-memory**: Fastest, reset when CLI exits
2. **Disk cache**: `~/.local/share/kurocode/models_cache.json`

Resolution order:
1. In-memory cache (skip if `--refresh`)
2. Network fetch → save to disk
3. Disk cache (offline fallback with warning)

## Error Handling

- **Network unavailable**: Falls back to disk cache with warning
- **Model not found**: Clear error message with hint
- **No models found**: Message indicating no matches
