<!-- Generated from website/src/content/docs/docs/integrations/litellm.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# LiteLLM

Trace LiteLLM calls with cetra from Python, with the OpenInference LiteLLM instrumentation.

cetra can trace LiteLLM calls from Python through the OpenInference LiteLLM instrumentation.

Running the LiteLLM Proxy instead? See [LiteLLM Proxy](https://cetra.sh/docs/integrations/litellm-proxy/).

## Install

```bash
pip install "cetra-sdk[litellm]" litellm
```

## Set up

```python
import litellm

import cetra

cetra.init(instrument=["litellm"])

litellm.completion(
    model="gpt-4o-mini",
    messages=[{"role": "user", "content": "What is the weather in Paris?"}],
)

cetra.flush()
```

## What you get

cetra reads the spans of the OpenInference LiteLLM instrumentation with the OpenInference convention. See [Semantic conventions](https://cetra.sh/docs/concepts/conventions/).

## Limits

- No captured spans of this instrumentation are in the repository. The only check is an SDK test that imports the instrumentor when the extra is installed. This page does not say which fields a LiteLLM call fills.
- If a field is missing, look at the span's **Attributes** tab and compare it with [Span attributes](https://cetra.sh/docs/reference/attributes/).

## Next

- [Traces](https://cetra.sh/docs/tracing/traces/): read the trace as a tree and a timeline.
- [Cost and tokens](https://cetra.sh/docs/tracing/cost-and-tokens/): how cost is computed from the token counts.
- [Online rules](https://cetra.sh/docs/evaluation/online-rules/): score these traces automatically.
