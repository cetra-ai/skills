<!-- Generated from website/src/content/docs/docs/tracing/metadata-and-tags.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# Metadata, tags, environments and releases

Label your traces with metadata, tags, an environment and a release, and use those labels to filter.

Use these four labels to slice your traces by customer, feature, deployment or version.

| Label | What it is for | Shape |
|---|---|---|
| Metadata | Facts about a request that you want to read or filter on: a plan, a customer, a feature flag. | Key and value pairs |
| Tags | Short labels that mark a trace: `beta`, `vip`, `retry`. | A list of strings |
| Environment | The deployment: `production`, `staging`, `dev`. | One string |
| Release | The version of your code: a Git SHA or a version number. | One string |

## Set them

**Python**

Metadata and tags go in a context block. Every span started inside the block carries them. Environment and release are set once, in `init()`.

```python
import cetra

cetra.init(environment="production", release="1.4.2")

@cetra.observe(name="turn")
def turn() -> None: ...

with cetra.context(tags=["beta"], metadata={"plan": "pro", "customer": "acme"}):
    turn()

cetra.flush()
```

To add metadata to one span, call `set_metadata` on a manual span.

```python
import cetra

cetra.init()

with cetra.span("rerank", kind="reranker") as s:
    s.set_metadata(model="bge")

cetra.flush()
```

**TypeScript**

```ts
import { observe, withContext } from "@cetra-ai/sdk";
```

```ts
const turn = observe(({ question }: { question: string }) => `answered: ${question}`, {
  name: "turn",
});
withContext({ sessionId: "chat-42", userId: "u-17", tags: ["beta"] }, () =>
  turn({ question: "why?" }),
);
```

Pass `environment` and `release` to `init()`, or set `CETRA_ENVIRONMENT` and `CETRA_RELEASE`.

**OpenTelemetry**

Set these attributes on a span: `cetra.metadata.<key>` for each metadata entry, `cetra.tags` for the tags, and `cetra.environment` and `cetra.release`. cetra also reads the resource attributes `deployment.environment.name` and `service.version`.

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

with tracer.start_as_current_span("turn") as span:
    span.set_attribute("cetra.metadata.customer", "acme")
    span.set_attribute("cetra.tags", ["beta"])
    span.set_attribute("cetra.environment", "production")
    span.set_attribute("cetra.release", "1.4.2")

provider.shutdown()
```

Metadata keys use letters, digits, `_`, `.` and `-`. The SDK skips a key that does not. Keep personal data out of metadata, or mask it. See [Masking, sampling and truncation](https://cetra.sh/docs/tracing/privacy-controls/).

The environments `cetra-eval` and `cetra-playground` are reserved for cetra's own evaluator and playground runs, and `experiment` is the default for experiment runs. cetra renames `cetra-eval` and `cetra-playground` on spans you send to `cetra-eval-external` and `cetra-playground-external`.

## In the UI

The trace list has columns for tags and environment. The trace header shows the tags, environment and release. The **Attributes** tab of a span shows its metadata.

## Filter

| Filter | Finds |
|---|---|
| `metadata['customer'] = 'acme'` | Traces with that metadata value. |
| `tags @> ['beta']` | Traces with the tag `beta`. |
| `environment = 'production'` | Traces from one environment. |
| `release = '1.4.2'` | Traces from one release. |

See the [filter language](https://cetra.sh/docs/reference/filter-language/).

## Limits

- A trace's metadata comes from its root span. Keys from other spans are added when the root does not have them.
- A trace's tags are the tags of all its spans together.
- Metadata values are stored as text. A number is stored as its text form.
