# history Command

The `history` command manages conversation history stored in SQLite.

## Usage

```bash
kurocode history <subcommand>
```

## Subcommands

| Subcommand | Description |
|------------|-------------|
| `list` | List recent conversations |
| `view <conv_id>` | View a specific conversation |
| `export <conv_id>` | Export conversation to markdown |

## Examples

### List Recent Conversations

```bash
kurocode history list
```

Output:

```
I [abc123-def456-...] 2024-01-15 14:32:10 - openai/gpt-4o-mini - Interactive Chat
I [xyz789-...] 2024-01-15 12:15:00 - anthropic/claude-3-haiku - Interactive Chat
```

### List with Limit

```bash
kurocode history list --limit 50
```

### View Conversation

```bash
kurocode history view abc123-def456-...
```

Output:

```
[2024-01-15 14:32:10] **USER**:
What is Python?

[2024-01-15 14:32:15] **ASSISTANT**:
Python is a high-level programming language...

[2024-01-15 14:32:25] **USER**:
How do I read a file?
```

### Export to Markdown

```bash
kurocode history export abc123-def456-... --format markdown > transcript.md
```

Output file:

```markdown
**User**:
What is Python?

**Assistant**:
Python is a high-level programming language...
```

## Data Storage

Conversations are stored in SQLite at:

```
~/.local/share/kurocode/history.db
```

Override with `KUROCODE_DB_PATH` environment variable.

### Schema

```sql
CREATE TABLE conversations (
    id         TEXT PRIMARY KEY,
    title      TEXT NOT NULL,
    model      TEXT NOT NULL,
    created_at INTEGER NOT NULL
);

CREATE TABLE messages (
    id              TEXT PRIMARY KEY,
    conversation_id TEXT NOT NULL REFERENCES conversations(id),
    role            TEXT NOT NULL CHECK(role IN ('system', 'user', 'assistant')),
    content         TEXT NOT NULL,
    created_at      INTEGER NOT NULL
);
```

## Output Formats

### Rich (default)

Colored output with timestamps.

### Plain

No ANSI codes, suitable for logging.

### JSON

```json
[
  {
    "id": "abc123-...",
    "title": "Interactive Chat",
    "model": "openai/gpt-4o-mini",
    "created_at": 1705326730
  }
]
```
