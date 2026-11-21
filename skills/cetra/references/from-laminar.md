<!-- Generated from website/src/content/docs/docs/integrations/from-laminar.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# Migrating from Laminar

Keep the Laminar SDK and send its spans to cetra: what carries over (sessions, users, tags, metadata) and what changes.

cetra reads the spans that the Laminar Python SDK produces, so you can keep your Laminar instrumentation while you move.

## Point the exporter at cetra

Laminar can export OpenTelemetry over HTTP to a base URL and a port, and sends your project key as a bearer token. The fixtures were captured with these settings, with the host and the key changed.

```python
from lmnr import Laminar

Laminar.initialize(
    project_api_key="<SK_CETRA_KEY>",
    base_url="http://localhost",
    http_port=8080,
    force_http=True,
)
```

Replace `<SK_CETRA_KEY>` with your `sk-cetra-` API key.

## What carries over

The fixtures show the Laminar SDK 0.8.8 producing these, and cetra reading them:

- `@observe` spans become `chain` spans, and LLM spans become `llm` spans, with the OpenAI messages, the model, the token counts including cache and reasoning tokens, and the cost.
- Session ID, user ID, tags and metadata set on `@observe` become the cetra session, user, tags and metadata. The LLM child span carries the session and user too.
- Tool spans keep their input as JSON and their output as text.
- Streamed completions are captured.

## What changes

- Laminar's evaluations, datasets and scores are not spans. Use cetra's [Scores](https://cetra.sh/docs/evaluation/scores/), [Datasets](https://cetra.sh/docs/evaluation/datasets/) and [Experiments](https://cetra.sh/docs/evaluation/experiments/).
- Spans arrive in cetra under cetra's own names and fields. See [Span attributes](https://cetra.sh/docs/reference/attributes/) for the Laminar attributes cetra reads.

## Limits

- The fixtures are Python only.
- The settings above export over HTTP. Laminar's default export is gRPC. The fixtures were captured over HTTP.

## Next

- [Traces](https://cetra.sh/docs/tracing/traces/): read the trace as a tree and a timeline.
- [Cost and tokens](https://cetra.sh/docs/tracing/cost-and-tokens/): how cost is computed from the token counts.
- [Online rules](https://cetra.sh/docs/evaluation/online-rules/): score these traces automatically.
