<!-- Generated from website/src/content/docs/docs/get-started/opentelemetry.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# Send OpenTelemetry

Send traces to cetra from any OpenTelemetry setup, in Python, Node.js, Go, Java or through a Collector.

Use plain OpenTelemetry when your application already has it, or when it is written in a language without a cetra SDK.

## Endpoints

| Protocol | Endpoint |
|---|---|
| OTLP/HTTP | `http://<HOST>:8080/v1/traces` or `http://<HOST>:4318/v1/traces` |
| OTLP/gRPC | `<HOST>:4317` |

Authenticate with an API key in a header: `Authorization: Bearer sk-cetra-…`. cetra accepts protobuf and JSON bodies, and gzip compression.

## Python

Install the OpenTelemetry SDK and the OTLP/HTTP exporter:

```bash
pip install opentelemetry-sdk opentelemetry-exporter-otlp-proto-http
export CETRA_API_KEY=sk-cetra-<32_LETTERS_OR_DIGITS>
export OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:8080
```

The exporter reads the endpoint from `OTEL_EXPORTER_OTLP_ENDPOINT` and adds `/v1/traces` itself.

```python
import os

from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

provider = TracerProvider()
provider.add_span_processor(
    BatchSpanProcessor(
        OTLPSpanExporter(headers={"Authorization": f"Bearer {os.environ['CETRA_API_KEY']}"})
    )
)
tracer = provider.get_tracer("my-app")

with tracer.start_as_current_span("chat") as span:
    span.set_attribute("gen_ai.provider.name", "openai")
    span.set_attribute("gen_ai.request.model", "gpt-4o-mini")
    span.set_attribute("gen_ai.usage.input_tokens", 12)
    span.set_attribute("gen_ai.usage.output_tokens", 3)

provider.shutdown()  # flushes the spans before the script ends
```

## Node.js

```bash
npm install @opentelemetry/sdk-trace-node @opentelemetry/exporter-trace-otlp-proto
export CETRA_API_KEY=sk-cetra-<32_LETTERS_OR_DIGITS>
export OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:8080
```

```ts
import { OTLPTraceExporter } from "@opentelemetry/exporter-trace-otlp-proto";
import { BatchSpanProcessor } from "@opentelemetry/sdk-trace-node";
import { NodeTracerProvider } from "@opentelemetry/sdk-trace-node";

const provider = new NodeTracerProvider({
  spanProcessors: [
    new BatchSpanProcessor(
      new OTLPTraceExporter({ headers: { Authorization: `Bearer ${process.env.CETRA_API_KEY}` } }),
    ),
  ],
});
provider.register();

const tracer = provider.getTracer("my-app");
const span = tracer.startSpan("chat");
span.setAttribute("gen_ai.provider.name", "openai");
span.setAttribute("gen_ai.request.model", "gpt-4o-mini");
span.end();

await provider.shutdown();
```

## Go

Use the OTLP/HTTP exporter and set the endpoint and header through the standard variables:

```bash
export OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:8080
export OTEL_EXPORTER_OTLP_HEADERS="Authorization=Bearer sk-cetra-…"
```

```go
exporter, err := otlptracehttp.New(ctx) // go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracehttp
if err != nil {
	log.Fatal(err)
}
provider := sdktrace.NewTracerProvider(sdktrace.WithBatcher(exporter))
defer provider.Shutdown(ctx)
otel.SetTracerProvider(provider)
```

## Java

With the OpenTelemetry Java agent, set the same variables:

```bash
export OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:8080
export OTEL_EXPORTER_OTLP_PROTOCOL=http/protobuf
export OTEL_EXPORTER_OTLP_HEADERS="Authorization=Bearer sk-cetra-…"
java -javaagent:opentelemetry-javaagent.jar -jar app.jar
```

## OpenTelemetry Collector

If your application already sends to a Collector, add an `otlphttp` exporter with the header:

```yaml
exporters:
  otlphttp/cetra:
    endpoint: http://localhost:8080
    headers:
      Authorization: Bearer ${env:CETRA_API_KEY}

service:
  pipelines:
    traces:
      exporters: [otlphttp/cetra]
```

## What cetra reads

cetra reads the OpenTelemetry GenAI attributes (`gen_ai.*`) and the OpenInference conventions, and records inputs, outputs, token usage and cost from them. Spans without these attributes are stored as they are. See [Semantic conventions](https://cetra.sh/docs/concepts/conventions/) for every convention cetra reads.
