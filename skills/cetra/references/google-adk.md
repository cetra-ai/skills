<!-- Generated from website/src/content/docs/docs/integrations/google-adk.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# Google ADK

Trace Google ADK agents with cetra from Python: invocations, agent runs, model calls and tool calls, over OpenTelemetry.

cetra reads the spans that Google's Agent Development Kit emits itself, and the spans of OpenInference's ADK instrumentation.

## Point the exporter at cetra

ADK emits OpenTelemetry spans through the global tracer provider. Install a provider that exports to cetra before you run the agent. This is the setup the fixtures were captured with, with the endpoint and the key changed.

```python
from opentelemetry import trace
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
trace.set_tracer_provider(provider)

# Build and run your ADK agent as usual, then:
provider.shutdown()
```

Replace `<SK_CETRA_KEY>` with your `sk-cetra-` API key. See [Send OpenTelemetry](https://cetra.sh/docs/get-started/opentelemetry/).

## What you get

With ADK's own spans, a run with one tool produces:

- an `invocation` span, and an `agent` span for the agent;
- a `chain` span for each `call_llm`, with the model, the instructions and the tool declarations;
- an `llm` span for each model call, with the provider, token counts and cost;
- a `tool` span for each `execute_tool`.

The session ID of the ADK session is the cetra session. Streaming runs and runs with thinking enabled are captured, and reasoning tokens are added to the output count.

## OpenInference's ADK instrumentation

You can use `openinference-instrumentation-google-adk` instead. Its spans carry the session and the user, and the `call_llm` span is the `llm` span.

## Limits

- The fixtures were captured with `google-adk` 2.14.0 and `google-genai` 2.34.0, and with `openinference-instrumentation-google-adk` 1.1.0.
- `cetra-sdk` has no `instrument` name for ADK.

## Next

- [Traces](https://cetra.sh/docs/tracing/traces/): read the trace as a tree and a timeline.
- [Cost and tokens](https://cetra.sh/docs/tracing/cost-and-tokens/): how cost is computed from the token counts.
- [Online rules](https://cetra.sh/docs/evaluation/online-rules/): score these traces automatically.
