# Architecture

This document describes the code structure and design decisions in KuroCode.

## Overview

KuroCode follows a layered architecture:

```
┌─────────────────────────────────────────────────────────┐
│                    CLI Layer (Click)                     │
│  __main__.py, commands/*.py                             │
├─────────────────────────────────────────────────────────┤
│                   Core Layer                            │
│  session.py, renderer.py, model_registry.py            │
├─────────────────────────────────────────────────────────┤
│                 Infrastructure Layer                    │
│  config.py, openrouter_client.py, store.py             │
├─────────────────────────────────────────────────────────┤
│                  External Services                      │
│  OpenRouter API, SQLite, Filesystem                     │
└─────────────────────────────────────────────────────────┘
```

## Layer Responsibilities

### CLI Layer

**Files**: `__main__.py`, `commands/*.py`

- Parses user input via Click
- Handles global options (`--output-format`, `--no-stream`)
- Routes to appropriate async functions
- Manages CLI context (`CliContext`)

### Core Layer

**Files**: `core/*.py`

Pure business logic with minimal dependencies:

- **Session**: In-memory conversation model (no I/O)
- **Renderer**: Output formatting (Rich/Plain/JSON)
- **ModelRegistry**: Model fetching with caching

### Infrastructure Layer

**Files**: `infra/*.py`

External integrations:

- **Config**: Pydantic settings + TOML loading
- **OpenRouterClient**: Async HTTP client with SSE streaming
- **Store**: SQLite conversation persistence

## Key Design Decisions

### 1. Async Everywhere

All I/O operations are async for future scalability:

```python
async def run_ask(ctx: CliContext, prompt: str, model: str) -> None:
    async with OpenRouterClient(ctx.config) as client:
        async for chunk in client.stream_chat(...):
            ...
```

### 2. Context Object Pattern

A single `CliContext` dataclass flows through all commands:

```python
@dataclass
class CliContext:
    renderer: Renderer
    config: Settings
    no_stream: bool
```

Benefits:
- Easy testability (mock context)
- Consistent access to config/rendering
- No global state

### 3. Layered Configuration

Multiple sources with clear priority:

```
Environment Variables > .env > TOML [default] > TOML [profiles.X] > Defaults
```

### 4. Exception Hierarchy

Typed exceptions enable precise error handling:

```
KurocodeError
├── ConfigError
├── StoreError
└── APIError
     ├── AuthError
     └── RateLimitError
```

### 5. Renderer Abstraction

Output format is decoupled from business logic:

```python
# Business logic doesn't know about Rich
ctx.renderer.stream_token(delta)

# Renderer handles format differences
if self._fmt == OutputFormat.JSON:
    print(json.dumps({"type": "token", "data": token}))
else:
    console.print(token, end="")
```

## Module Details

### Session (`core/session.py`)

Pure dataclass with no I/O:

```python
@dataclass
class Session:
    model_id: str
    id: str  # UUID
    messages: list[Message]
    created_at: float

    def add_user_message(self, content: str) -> Message: ...
    def token_estimate(self) -> int: ...  # char/4 heuristic
    def to_openrouter_messages(self) -> list[dict]: ...
```

### ModelRegistry (`core/model_registry.py`)

Two-level cache (memory → disk):

```python
class ModelRegistry:
    async def fetch(self, force_refresh: bool = False) -> list[ModelInfo]:
        # 1. Memory cache (skip if force_refresh)
        # 2. Network fetch → save to disk
        # 3. Disk cache fallback (offline)
```

### OpenRouterClient (`infra/openrouter_client.py`)

Async context manager with typed responses:

```python
async with OpenRouterClient(settings) as client:
    # Non-streaming (with tenacity retry)
    resp = await client.chat(messages, model)
    
    # Streaming
    async for chunk in client.stream_chat(messages, model):
        yield chunk
```

### Store (`infra/store.py`)

Async SQLite with migrations:

```python
async with ConversationStore(db_path) as store:
    cid = await store.create_conversation(title, model)
    await store.add_message(cid, "user", content)
    msgs = await store.get_messages(cid)
```

## Data Flow

### ask Command

```
User Input
    │
    ▼
Click Parser ──► CliContext
    │
    ▼
run_ask()
    │
    ├─► Session.add_user_message()
    │
    ├─► OpenRouterClient.stream_chat()
    │       │
    │       ▼
    │   SSE Stream ──► StreamChunk
    │       │
    │       ▼
    │   Renderer.stream_token()
    │
    ▼
[Exit]
```

### chat Command

```
User Input
    │
    ▼
run_chat()
    │
    ├─► ConversationStore.create_conversation()
    │
    ├─► REPL Loop:
    │       │
    │       ├─► Slash Command → handle_slash_command()
    │       │
    │       └─► Normal Input:
    │               │
    │               ├─► Session.add_user_message()
    │               ├─► Store.add_message()
    │               ├─► Client.stream_chat()
    │               ├─► Renderer.stream_token()
    │               ├─► Session.add_assistant_message()
    │               └─► Store.add_message()
    │
    ▼
[Exit]
```

## Error Handling Flow

```
API Error
    │
    ▼
_raise_for_status()
    │
    ├─► 401/403 → AuthError
    ├─► 429 → RateLimitError
    └─► 5xx → APIError
    │
    ▼
AsyncRetrying (tenacity)
    │
    ├─► Retryable? → Retry with backoff
    └─► Non-retryable → Raise
    │
    ▼
KuroCodeGroup.invoke()
    │
    ▼
Renderer.error(msg, hint)
    │
    ▼
sys.exit(1)
```

## Testing Strategy

| Layer | Test Type | Dependencies |
|-------|-----------|--------------|
| Core | Unit | None |
| Commands | CLI | Click runner |
| Infrastructure | Integration | Mocked HTTP |
| End-to-End | Manual | Real API |

## Performance Considerations

1. **Model Cache**: In-memory + disk avoids repeated API calls
2. **Streaming**: Tokens displayed immediately, no buffering
3. **Async I/O**: Non-blocking network/database operations
4. **Lazy Loading**: Commands imported as needed
