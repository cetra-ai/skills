<!-- Generated from website/src/content/docs/docs/integrations/from-langfuse.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# Migrating from Langfuse

Keep the Langfuse Python SDK and send its spans to cetra: what carries over (sessions, users, tags, metadata, cost) and what changes.

cetra reads the spans that the Langfuse Python SDK produces, so you can keep your Langfuse instrumentation while you move.

## Point the exporter at cetra

The Langfuse SDK builds on OpenTelemetry and installs its own tracer provider. By default it exports to Langfuse. To send its spans to cetra, add an OTLP exporter to that provider after you create the client.

```python
from langfuse import Langfuse
from opentelemetry import trace
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace.export import BatchSpanProcessor

langfuse = Langfuse()

trace.get_tracer_provider().add_span_processor(
    BatchSpanProcessor(
        OTLPSpanExporter(
            endpoint="http://localhost:8080/v1/traces",
            headers={"Authorization": "Bearer <SK_CETRA_KEY>"},
        )
    )
)
```

Replace `<SK_CETRA_KEY>` with your `sk-cetra-` API key.

## What carries over

The fixtures show the Langfuse SDK 5.1.0 producing these, and cetra reading them:

- `@observe` spans, with `as_type="tool"` becoming a `tool` span and generations becoming `llm` spans.
- The input and output of decorated functions, and the messages of the OpenAI drop-in.
- Session ID, user ID and tags, set at trace level, become the cetra session, user and tags. Trace metadata becomes metadata.
- The model, model parameters and `usage_details`, including cached and reasoning tokens.
- A reported cost (`cost_details`) is kept as the reported cost, and cetra does not replace it with a computed one, except a total of exactly 0 on a span that has tokens, which is priced from the price table (see [Cost and tokens](https://cetra.sh/docs/tracing/cost-and-tokens/)).
- `completion_start_time` gives the time to first token.
- An observation with level `ERROR` has the status `error`.

## What changes

- Scores and datasets are not spans, so they do not move with the exporter. Write scores with the cetra SDK or the API. See [Scores](https://cetra.sh/docs/evaluation/scores/).
- Spans arrive in cetra under cetra's own names and fields. See [Span attributes](https://cetra.sh/docs/reference/attributes/) for the Langfuse attributes cetra reads.

## Limits

- The fixtures are Python only.
- The exporter wiring above is not covered by a test. The fixtures cover the spans, not their delivery.

## Next

- [Traces](https://cetra.sh/docs/tracing/traces/): read the trace as a tree and a timeline.
- [Cost and tokens](https://cetra.sh/docs/tracing/cost-and-tokens/): how cost is computed from the token counts.
- [Online rules](https://cetra.sh/docs/evaluation/online-rules/): score these traces automatically.
