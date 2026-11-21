<!-- Generated from website/src/content/docs/docs/integrations/opentelemetry-genai.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# OpenTelemetry GenAI instrumentations

Send the official OpenTelemetry GenAI instrumentation packages to cetra: OpenAI and Google Gen AI, with GenAI semantic conventions.

cetra reads the OpenTelemetry GenAI semantic conventions, so the official `opentelemetry-instrumentation-*` packages work with a plain OTLP exporter.

## Point the exporter at cetra

Add an OTLP exporter to your provider, then instrument the library. This is the setup the fixtures were captured with, with the endpoint and the key changed.

```python
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.instrumentation.openai_v2 import OpenAIInstrumentor
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

Replace `<SK_CETRA_KEY>` with your `sk-cetra-` API key. See [Send OpenTelemetry](https://cetra.sh/docs/get-started/opentelemetry/) for the endpoint and the ports.

## What carries over

- The `gen_ai.operation.name` decides the span kind: `chat` and `generate_content` are `llm`, `embeddings` is `embedding`, `execute_tool` is `tool`, `invoke_agent` is `agent`, `retrieval` is `retriever` and `invoke_workflow` is `chain`.
- Messages come from `gen_ai.input.messages` and `gen_ai.output.messages`, and when the span does not have them, from the operation-details event. The older `gen_ai.*.message` events are read as well.
- System instructions, tool definitions, the token counts including cache and reasoning tokens, request parameters and finish reasons are read.
- The time to first token is read from `gen_ai.response.time_to_first_chunk`.

## Limits

- The captured fixtures are two Python packages: `opentelemetry-instrumentation-openai-v2` 2.4b0 and `opentelemetry-instrumentation-google-genai` 1.1b1.
- Some instrumentations send prompts and completions as log events instead of span attributes. cetra drops log events, so those prompts do not arrive. Turn on the instrumentation's option to record content on spans. See [OTLP endpoint](https://cetra.sh/docs/reference/otlp/).

## Next

- [Traces](https://cetra.sh/docs/tracing/traces/): read the trace as a tree and a timeline.
- [Cost and tokens](https://cetra.sh/docs/tracing/cost-and-tokens/): how cost is computed from the token counts.
- [Online rules](https://cetra.sh/docs/evaluation/online-rules/): score these traces automatically.
