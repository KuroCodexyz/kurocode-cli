# chat Command

The `chat` command launches an interactive REPL for multi-turn conversations with AI models. It supports session persistence, model switching, and slash commands.

## Usage

```bash
kurocode chat
```

## Options

| Option | Default | Description |
|--------|---------|-------------|
| `--model` | `openai/gpt-4o-mini` | Model to use for the response |
| `--resume` | - | Resume an existing conversation by ID |

## Global Options (before `chat`)

| Option | Description |
|--------|-------------|
| `--output-format <rich\|plain\|json>` | Output format |
| `--no-stream` | Wait for full response instead of streaming |
| `--profile <name>` | Use a specific configuration profile |

## Examples

### Start New Chat

```bash
kurocode chat
```

### Start with Specific Model

```bash
kurocode chat --model anthropic/claude-3-haiku
```

### Resume Previous Conversation

```bash
# List conversations to find ID
kurocode history list

# Resume by ID
kurocode chat --resume abc123-def456
```

## Interactive Mode

When in the chat REPL:

- **Input**: Type your message and press Enter
- **Exit**: Press `Ctrl+D` or `Ctrl+C` at the prompt
- **Interrupt**: Press `Ctrl+C` during AI response to stop streaming

## Slash Commands

| Command | Description |
|---------|-------------|
| `/help` | Show available commands |
| `/switch <model_id>` | Switch to a different model |
| `/model <model_id>` | Alias for `/switch` |
| `/clear` | Clear session context (keep history) |
| `/save <filename>` | Save session transcript to markdown |

### Examples

```
> /switch anthropic/claude-3-opus
Switched model to: anthropic/claude-3-opus

> /clear
Session cleared.

> /save conversation.md
✓ Saved session to conversation.md
```

## Session Persistence

- Conversations are automatically saved to SQLite
- Use `--resume` to continue a previous session
- View history with `kurocode history list`

## Response Handling

### Streaming (default)

Tokens are displayed as they arrive from the API.

### Non-Streaming

```bash
kurocode --no-stream chat
```

Waits for complete response before displaying.

### Interrupt Handling

- Press `Ctrl+C` during streaming
- Partial response is saved to history
- Message: `[Stream interrupted. Saving partial response...]`

## Example Session

```
$ kurocode chat --model openai/gpt-4o-mini
I Started new chat with openai/gpt-4o-mini. Type /help for commands.

> What is Python?
[AI response about Python...]

> How do I read a file?
[AI response about file reading...]

> /switch anthropic/claude-3-haiku
Switched model to: anthropic/claude-3-haiku

> What's the weather?
[AI response about weather...]

> /save python_session.md
✓ Saved session to python_session.md

> [Ctrl+D]
```
