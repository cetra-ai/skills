<!-- Generated from website/src/content/docs/docs/integrations/crewai.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# CrewAI

Trace CrewAI with cetra from Python, with the OpenInference CrewAI instrumentation.

cetra can trace CrewAI crews, agents, tasks and tool uses from Python through the OpenInference CrewAI instrumentation.

## Install

```bash
pip install "cetra-sdk[crewai]" crewai
```

## Set up

```python
import cetra

cetra.init(instrument=["crewai"])

# Build and run your crew as usual. CrewAI runs are traced from here on.
```

## What you get

cetra reads the spans of the OpenInference CrewAI instrumentation with the OpenInference convention. See [Semantic conventions](https://cetra.sh/docs/concepts/conventions/).

## Limits

- No captured spans of this instrumentation are in the repository. The only check is an SDK test that imports the instrumentor when the extra is installed. This page does not say which CrewAI operations are traced.
- If a field is missing, look at the span's **Attributes** tab and compare it with [Span attributes](https://cetra.sh/docs/reference/attributes/).

## Next

- [Traces](https://cetra.sh/docs/tracing/traces/): read the trace as a tree and a timeline.
- [Cost and tokens](https://cetra.sh/docs/tracing/cost-and-tokens/): how cost is computed from the token counts.
- [Online rules](https://cetra.sh/docs/evaluation/online-rules/): score these traces automatically.
