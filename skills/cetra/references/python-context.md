<!-- Generated from website/src/content/docs/docs/reference/python/context.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# Context (Python)

Attach sessions, users, tags and metadata to every span, send them downstream as baggage and carry them across threads.

Use `cetra.context` to group traces by session and user, and to carry that context across threads.

## Context and baggage

`cetra.context(session_id=..., user_id=..., tags=[...], metadata={...})` is a sync and async context manager that applies those values to every span started inside it, ours and the instrumentors' alike. The values live in the OpenTelemetry context, so they follow `contextvars` (and `copy_context()`) across tasks.

Nested blocks merge: the inner `session_id`/`user_id` win when given, tags are the union (outer first, no duplicates), metadata merges by key with the inner value winning. Metadata values are masked when the block is entered.

```python
import cetra

cetra.init(api_key="sk-cetra-test", environment="production", release="1.4.2")

@cetra.observe(name="turn")
def turn() -> None: ...

with cetra.context(session_id="chat-42", user_id="u-17", tags=["beta"], metadata={"plan": "pro"}):
    turn()

cetra.flush()
```

Every span of the block then carries `cetra.session.id`, `cetra.user.id`, `cetra.tags`, `cetra.metadata.plan`, and, when set at `init()`, `cetra.environment` and `cetra.release`. Metadata keys must match `[A-Za-z0-9_.-]+`; a key that does not is skipped (logged at debug level with its length only).

`context(prompt=...)` takes a `Prompt` from `get_prompt` and stamps its name as `cetra.prompt.name` and its version as `cetra.prompt.version` on every span of the block; a fallback prompt has no version, so only the name is written. A nested block without a prompt keeps the outer one, and an inner prompt replaces it. Any other value is ignored and logged at debug level. The [Prompts](https://cetra.sh/docs/evaluation/prompts/) page shows the whole flow.

`context(..., propagate=True)` additionally writes the session and user ids to W3C **baggage** as `cetra.session.id` and `cetra.user.id`. It is opt-in because baggage is sent as an HTTP header on every outgoing request of the app, to third-party APIs included. Tags and metadata never go into baggage. A downstream service running this SDK picks the ids up from baggage without calling `context()` itself.

## Threads

The SDK does not patch `threading` or executors. The OpenTelemetry context, including `context()` values and the current span, does not cross a thread boundary by itself, so a span started in a worker thread would be a detached root without them. Copy the context explicitly:

```python
import threading
from contextvars import copy_context

import cetra

cetra.init(api_key="sk-cetra-test")

@cetra.observe(name="lookup")
def lookup(key: str) -> str:
    return "found"

def work() -> None:
    lookup("pricing")

with cetra.span("request", input="why?"):
    ctx = copy_context()
    worker = threading.Thread(target=ctx.run, args=(work,))
    worker.start()

worker.join()
cetra.flush()
```

For thread pools, `ThreadPoolExecutor(..., initializer=...)` cannot capture a per-task context this way; OpenTelemetry's [opentelemetry-instrumentation-threading](https://pypi.org/project/opentelemetry-instrumentation-threading/) instrumentor patches `threading` for you instead.
