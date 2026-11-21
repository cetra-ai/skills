<!-- Generated from website/src/content/docs/docs/reference/python/opentelemetry.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# OpenTelemetry (Python)

Enable instrumentors, work next to an existing OpenTelemetry setup, mask payloads and know the limits of the Python SDK.

Use these settings to capture spans from LLM libraries, to share a process with other OpenTelemetry code and to keep content out of your traces.

## Instrumentation

`init(instrument=[...])` enables [OpenInference](https://github.com/Arize-ai/openinference)
instrumentors by name, from the fixed registry in the [install table](https://cetra.sh/docs/reference/python/#install):

```python
cetra.init(api_key="sk-cetra-test", instrument=["openai", "anthropic"])
```

An unknown name raises `ConfigError` listing the known names; a name whose
package is missing raises `ConfigError` naming the extra to install (for
example `instrument: 'openai' needs \`pip install cetra-sdk[openai]\``). Nothing
is installed when a name fails: the error surfaces from `init()` itself.

The instrumentors are created with our tracer provider, so their spans nest
under ours and carry the `context()` values. A library you already
instrumented yourself is left alone (one debug line). `shutdown()` uninstruments
everything the SDK enabled. Our provider raises the OTel attribute limit to
10 000, so flattened message attributes are not clipped.

## Existing OpenTelemetry setups

By default `init()` builds its own `TracerProvider` and never registers it
globally: your OTel setup is untouched, and instrumentor spans reach cetra
because the SDK binds them to our provider. The OTel *context* is always the
global one, so spans nest correctly in both modes.

With `use_global_provider=True`:

- an app's SDK `TracerProvider` already set as global takes our span processor
  (the app keeps its sampler: a `sample_rate` below 1 logs one warning and is
  ignored);
- when nothing is registered yet, our provider becomes the global one;
- any other global provider cannot take processors: the SDK logs one warning
  and keeps its own.

In global mode other libraries create spans on the same provider, so the
default export filter keeps only the LLM-related ones: spans whose
instrumentation scope is `cetra` or `ai`, or with an attribute key starting
with `cetra.`, `gen_ai.`, `llm.`, `openinference.`, `ai.`, `traceloop.`,
`langfuse.`, `lmnr.` or `gcp.vertex.agent.`, or with an `input.value` /
`output.value` key. Pass your own callable as `export_filter=` to replace that
rule in either mode: it receives the finished span and returns a boolean; if
it raises, the span is exported.

`cetra.span_processor()` returns our span processor for apps that build their
own OTel pipeline, and `cetra.tracer_provider()` returns the provider in use
(a no-op provider before `init()`, when disabled or after `shutdown()`), for
libraries that accept a provider or tracer.

## Masking and `capture_content`

`mask` is a callable applied at capture time to every value the SDK records —
inputs, outputs, metadata values, LLM messages — before serialization:

```python
def redact(value): ...  # return the value with secrets removed

cetra.init(api_key="sk-cetra-test", mask=redact)
```

At export time a second pass rewrites spans from *other* instrumentation
scopes (the instrumentors'): for each attribute under a payload key —
`cetra.input`, `cetra.output`, `gen_ai.input.messages`,
`gen_ai.output.messages`, `gen_ai.system_instructions`, `input.value`,
`output.value`, the `llm.input_messages.*` / `llm.output_messages.*` /
`gen_ai.prompt.*` / `gen_ai.completion.` prefixes and the other keys of spec
10 — the mask receives the decoded value (JSON-parsed when the string is a
JSON object or array) and the result is serialized back. Only string values
are masked; a non-string value under a payload key is deleted. If `mask`
raises, the value becomes `[cetra: mask failed]` — the SDK fails closed, and
a batch that cannot be rewritten is dropped, never exported unmasked.

`capture_content=False` records no payloads at all on our spans (no
input/output, no messages, no system instructions; metrics-like attributes —
models, params, token counts, cost — are kept), deletes the payload keys from
instrumentor spans, and configures the OpenInference instrumentors we create
with their hide-inputs/hide-outputs options.

## Serverless

`atexit` handlers do not run when a serverless platform freezes or terminates
an invocation. Call `cetra.flush()` at the end of every invocation (or
`cetra.shutdown()` when the instance is not reused); otherwise spans and
scores still sitting in the batch queue are lost.

## Known limits

- **Abandoned async generators are never exported.** An async generator that
  is neither exhausted nor `aclose()`d leaves its span unended — an async
  generator cannot be closed from a finalizer. `aclose()` it when you stop
  early.
- **`inspect.isasyncgenfunction` (and `isgeneratorfunction`) is false on a
  wrapped function.** `observe` detects generator functions at decoration
  time; decorating an already-wrapped async generator function traces it as a
  plain call whose output is not recorded.
- **In global mode, the app's own spans get `cetra.*` attributes.** Our
  processor stamps `cetra.session.id`, `cetra.user.id`, `cetra.tags`,
  `cetra.metadata.*`, `cetra.environment` and `cetra.release` on every span
  of the provider it is attached to, so the app's other exporters see them.
- **`OTEL_EXPORTER_OTLP_HEADERS` is sent to cetra too.** The OTel HTTP
  exporter merges `OTEL_EXPORTER_OTLP_HEADERS` and
  `OTEL_EXPORTER_OTLP_TRACES_HEADERS` from the environment into its request
  headers; headers meant for another OTLP backend reach the cetra server as
  well. Unset them if that matters.
- **`KeyboardInterrupt` and `asyncio.CancelledError` are not errors.** Only
  `Exception` gets the error status. A `BaseException` that is not an
  `Exception` — inside a `span()` / `llm_span()` block or raised through
  `@observe` (functions, coroutine functions and both generator wrappers)
  alike — ends the span with the status unset, records no output, and is
  re-raised unchanged.
- **Zero-argument functions and `None`**: a call with no arguments records
  `{}` as input; a `None` return records `null` as output. TypeScript records
  no input for no arguments and no output for `undefined` (return `null` there
  to record a JSON `null`).
- **Integral floats**: Python writes `1.0` as a double; TypeScript has one
  number type and writes an integral value as an int. The server reads both.
- **Large integers**: Python ints beyond ±(2^53−1) inside JSON payloads are
  written as JSON numbers; TypeScript `bigint` becomes a decimal string. As
  metadata values both are JSON strings.
- **Resource attributes**: Python reads `OTEL_RESOURCE_ATTRIBUTES`; TypeScript
  reads only `OTEL_SERVICE_NAME`.
- **Abandoned sync generators** that are garbage-collected half-consumed end
  with status ok (TypeScript has no finalizer and leaves them unexported).
- **Usage counts**: `set_usage` records any non-negative `int`, including
  values beyond 2^53; TypeScript `setUsage` records only safe integers.
- **Threads and serverless**: see [Threads](https://cetra.sh/docs/reference/python/context/#threads) and
  [Serverless](#serverless).
