<!-- Generated from website/src/content/docs/docs/integrations/openllmetry.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# OpenLLMetry (Traceloop)

Send OpenLLMetry and Traceloop instrumentation to cetra from any language, with the Traceloop SDK or the instrumentation packages.

cetra reads spans from OpenLLMetry's instrumentation packages and from the Traceloop SDK.

## Point the exporter at cetra

With the Traceloop SDK, set the endpoint to cetra. The SDK adds `/v1/traces` to the endpoint. The fixtures were captured with a call like this, with the endpoint changed.

```python
from traceloop.sdk import Traceloop

Traceloop.init(
    app_name="my-app",
    api_endpoint="http://localhost:8080",
    api_key="<SK_CETRA_KEY>",
)
```

Replace `<SK_CETRA_KEY>` with your `sk-cetra-` API key. If you use the `opentelemetry-instrumentation-*` packages without the Traceloop SDK, add an exporter to your provider as shown on [OpenInference](https://cetra.sh/docs/integrations/openinference/).

## What carries over

- `@workflow`, `@task` and tool spans become `chain` and `tool` spans. The workflow name goes into the span's metadata under `workflow`.
- Session and user IDs set with `Traceloop.set_association_properties` become the session and user. Other association properties become metadata.
- Flattened prompts and completions (`gen_ai.prompt.N` and `gen_ai.completion.N`) become input and output messages. The `llm.request.functions` attributes become tool definitions.
- Token counts, cache tokens and streamed time to first token are read from the LLM spans.
- If a span carries both OpenTelemetry GenAI messages and OpenLLMetry's flattened keys, cetra uses the GenAI ones.

## Limits

- The captured fixtures are Python, with OpenLLMetry 0.62.4. Other languages' packages are not captured here.
- For Anthropic, OpenLLMetry's prompt token count can exclude the cache. cetra adds the cache back.

## Next

- [Traces](https://cetra.sh/docs/tracing/traces/): read the trace as a tree and a timeline.
- [Cost and tokens](https://cetra.sh/docs/tracing/cost-and-tokens/): how cost is computed from the token counts.
- [Online rules](https://cetra.sh/docs/evaluation/online-rules/): score these traces automatically.
