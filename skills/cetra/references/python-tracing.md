<!-- Generated from website/src/content/docs/docs/reference/python/tracing.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# Tracing (Python)

Trace Python functions with observe, write spans by hand and record LLM calls with llm_span.

Use `observe`, `span` and `llm_span` to turn your Python code into spans.

## Decorator rules

`@cetra.observe` traces a function, coroutine function, generator function or async generator function as one span. Both forms work: `@cetra.observe` and `@cetra.observe(name=..., kind=..., capture_input=..., capture_output=..., reduce_output=...)`. The span name defaults to the function's qualified name; `kind` defaults to `"chain"` and must be one of `llm`, `embedding`, `tool`, `agent`, `chain`, `retriever`, `reranker`, `guardrail`, `evaluator`, `span`. An invalid `kind` raises `ValueError` at decoration time, before the function runs. A function decorated before `init()` is traced once the SDK starts: the runtime is looked up at call time.

**Input.** The bound arguments with defaults applied, as one JSON object keyed by parameter name. A first parameter named `self` or `cls` is dropped, `*args` becomes a list and `**kwargs` an object under their parameter names. `capture_input=False` records no input.

**Output.** A `str` return value is recorded as text; anything else is serialized to compact JSON (dataclasses, pydantic v2 models, enums, UUIDs, datetimes, `bytes` as base64 data URIs, mappings and sequences, with cycle and depth guards). Payloads over 1 MiB are cut and marked `…[cetra: truncated, original N bytes]`. `capture_output=False` records no output. A plain function that *returns* a generator is traced as a call: the returned object is passed through untouched, never consumed, and its output is not recorded.

**Async.** The span of a coroutine function starts when the coroutine is first awaited, not when it is created.

**Generators.** Detected at decoration time. The span starts at the generator's first step and is current only while the body runs. Exhaustion records the items: the joined string when every item is a `str`, otherwise a JSON list, or `reduce_output(items)` when given. Closing early (`break`, `close()`, `aclose()`) ends the span with status `ok` and the items yielded so far. A half-consumed sync generator that is garbage-collected is closed the same way; an abandoned async generator is never exported (see [Known limits](https://cetra.sh/docs/reference/python/opentelemetry/#known-limits)), so `aclose()` it if you stop early.

**Errors.** An exception raised by the function adds one `exception` event (`exception.type`, `exception.message`, `exception.stacktrace`), sets the span status to error with the message `"<type>: <message>"`, and is re-raised unchanged. A `GeneratorExit` is not an error.

## Manual spans and `llm_span`

`cetra.span(name, kind=..., input=..., metadata=...)` is a sync and async context manager. The body's return value is never recorded; the handle's setters write it instead.

```python
import cetra

cetra.init(api_key="sk-cetra-test")

with cetra.span("rerank", kind="reranker", input=["a", "b"]) as s:
    ranked = ["b", "a"]
    s.set_output(ranked)
    s.set_metadata(model="bge")

cetra.flush()
```

The handle (`cetra.Span`) exposes `set_input(value)`, `set_output(value)`, `set_metadata(**values)` and the `trace_id` / `span_id` properties (32 and 16 lowercase hex characters). Every method is a no-op after the span ended and never raises.

`cetra.llm_span(name, provider=..., model=...)` writes the GenAI attributes of a chat call, for providers without an instrumentor. `provider` and `model` are required non-empty strings (a bad one raises `ValueError` at construction).

```python
import cetra

cetra.init(api_key="sk-cetra-test")

with cetra.llm_span(
    "chat",
    provider="acme",
    model="acme-1",
    params={"temperature": 0.2},
    input_messages=[{"role": "user", "content": "hi"}],
) as s:
    s.set_output_messages([{"role": "assistant", "content": "hello"}])
    s.set_response_model("acme-1-2026-01")
    s.set_usage(input_tokens=12, output_tokens=3)
    s.set_cost(total_usd=0.0004)
    s.record_first_token()

cetra.flush()
```

Messages may use the canonical `{"role", "parts"}` form or the short form `{"role", "content": "<text>"}`, converted to one text part. On the `cetra.LlmSpan` handle:

| Setter | Attribute |
|---|---|
| `set_input_messages(messages)` | `gen_ai.input.messages` |
| `set_output_messages(messages)` | `gen_ai.output.messages` |
| `set_response_model(m)` | `gen_ai.response.model` |
| `set_usage(input_tokens=, output_tokens=, cache_read_tokens=, cache_write_tokens=, reasoning_tokens=)` | the `gen_ai.usage.*` counts; ints >= 0 only, `input_tokens` includes cache tokens |
| `set_cost(total_usd)` | `cetra.cost.total` (USD, finite, >= 0) |
| `record_first_token()` | `cetra.ttft_ns`; only the first call counts |
| `set_metadata(**values)` | `cetra.metadata.<key>`, like every span |

The inherited `set_input` / `set_output` remain available for non-chat payloads. With `capture_content=False` the models, params, usage, cost and metadata are kept, but no messages, system instructions or input/output payloads are written.
