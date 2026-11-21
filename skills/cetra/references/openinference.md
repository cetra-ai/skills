<!-- Generated from website/src/content/docs/docs/integrations/openinference.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# OpenInference

Send OpenInference instrumentation to cetra from any language: the Arize instrumentation libraries, pointed at the cetra endpoint.

cetra reads spans from the OpenInference instrumentation libraries, so you can use them with their own OpenTelemetry exporter.

## Point the exporter at cetra

The `cetra-sdk` `instrument` option already uses these libraries. To use them with your own OpenTelemetry setup, add an exporter that sends to cetra and pass its provider to the instrumentor. This is the setup the fixtures were captured with, with the endpoint and the key changed.

```python
from openinference.instrumentation.openai import OpenAIInstrumentor
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

provider = TracerProvider()
provider.add_span_processor(
    BatchSpanProcessor(
        OTLPSpanExporter(
            endpoint="http://localhost:8080/v1/traces",
            headers={"Authorization": "Bearer <SK_CETRA_KEY>"},
        )
    )
)
OpenAIInstrumentor().instrument(tracer_provider=provider)
```

Replace `<SK_CETRA_KEY>` with your `sk-cetra-` API key. See [Send OpenTelemetry](https://cetra.sh/docs/get-started/opentelemetry/) for the endpoint, the ports and the other languages.

## What carries over

cetra reads these OpenInference attributes: the span kind, the model and provider, the flattened input and output messages, the tool definitions, token counts including cache and reasoning tokens, and cost. See [Span attributes](https://cetra.sh/docs/reference/attributes/).

- Retriever documents become the span's output.
- Embedding vectors are dropped. Their texts are kept.
- A value of `__REDACTED__`, which OpenInference writes for hidden content, is treated as content that was not captured.
- A span of kind `LLM`, `EMBEDDING`, `TOOL`, `AGENT`, `CHAIN`, `RETRIEVER`, `RERANKER`, `GUARDRAIL` or `EVALUATOR` becomes the cetra span kind of the same name.

## Limits

- The captured fixtures are Python. The TypeScript helpers of `@cetra-ai/sdk` use the OpenInference libraries for OpenAI, Anthropic and LangChain, and are covered by SDK tests.
- The versions covered are in each fixture's `case.json`.

## Next

- [Traces](https://cetra.sh/docs/tracing/traces/): read the trace as a tree and a timeline.
- [Cost and tokens](https://cetra.sh/docs/tracing/cost-and-tokens/): how cost is computed from the token counts.
- [Online rules](https://cetra.sh/docs/evaluation/online-rules/): score these traces automatically.
