# ask Command

The `ask` command sends a single question to an AI model and returns the response. It supports streaming output and pipe-friendly JSON mode.

## Usage

```bash
kurocode ask "Your question here"
```

## Options

| Option | Default | Description |
|--------|---------|-------------|
| `--model` | `openai/gpt-4o-mini` | Model to use for the response |

## Global Options (before `ask`)

| Option | Description |
|--------|-------------|
| `--output-format <rich\|plain\|json>` | Output format |
| `--no-stream` | Wait for full response instead of streaming |
| `--profile <name>` | Use a specific configuration profile |

## Examples

### Basic Query

```bash
kurocode ask "Explain async/await in Python"
```

### Specify Model

```bash
kurocode ask "What is the capital of France?" --model anthropic/claude-3-haiku
```

### Pipe Input (stdin)

```bash
cat main.py | kurocode ask "Find the bug in this code"

# Or with echo
echo "How do I reverse a list in Python?" | kurocode ask
```

### JSON Output (for scripting)

```bash
kurocode --output-format json --no-stream ask "Explain async/await" | jq .content
```

### Non-Streaming Mode

```bash
kurocode --no-stream ask "Tell me a joke"
```

## Output Formats

### Rich (default)

Colored, formatted output with ANSI codes.

### Plain

No ANSI codes, suitable for logging or files.

```bash
kurocode --output-format plain ask "Hello"
```

### JSON

Machine-readable output:

```json
{"content": "Async/await in Python is..."}
```

## Error Handling

- **Empty prompt**: Exits with error message
- **Network errors**: Retries up to `max_retries` times (default: 3)
- **Authentication errors**: Clear error with hint to check API key
- **Rate limits**: Retry-after hint when available

## Interrupt Handling

Press `Ctrl+C` during streaming to interrupt. The partial response is discarded.
