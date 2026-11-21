<!-- Generated from website/src/content/docs/docs/integrations/dspy.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# DSPy

Trace DSPy with cetra from Python, with the OpenInference DSPy instrumentation.

cetra can trace DSPy modules and model calls from Python through the OpenInference DSPy instrumentation.

## Install

```bash
pip install "cetra-sdk[dspy]" dspy
```

## Set up

```python
import cetra

cetra.init(instrument=["dspy"])

# Configure and call your DSPy modules as usual. They are traced from here on.
```

## What you get

cetra reads the spans of the OpenInference DSPy instrumentation with the OpenInference convention. See [Semantic conventions](https://cetra.sh/docs/concepts/conventions/).

## Limits

- No captured spans of this instrumentation are in the repository. The only check is an SDK test that imports the instrumentor when the extra is installed. This page does not say which DSPy operations are traced.
- If a field is missing, look at the span's **Attributes** tab and compare it with [Span attributes](https://cetra.sh/docs/reference/attributes/).

## Next

- [Traces](https://cetra.sh/docs/tracing/traces/): read the trace as a tree and a timeline.
- [Cost and tokens](https://cetra.sh/docs/tracing/cost-and-tokens/): how cost is computed from the token counts.
- [Online rules](https://cetra.sh/docs/evaluation/online-rules/): score these traces automatically.
