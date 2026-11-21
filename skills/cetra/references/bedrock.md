<!-- Generated from website/src/content/docs/docs/integrations/bedrock.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# Amazon Bedrock

Trace Amazon Bedrock calls with cetra from Python, with the OpenInference Bedrock instrumentation.

cetra can trace Amazon Bedrock calls from Python through the OpenInference Bedrock instrumentation.

## Install

```bash
pip install "cetra-sdk[bedrock]" boto3
```

## Set up

```python
import boto3

import cetra

cetra.init(instrument=["bedrock"])

client = boto3.client("bedrock-runtime")
client.converse(
    modelId="anthropic.claude-3-5-sonnet-20240620-v1:0",
    messages=[{"role": "user", "content": [{"text": "What is the weather in Paris?"}]}],
)

cetra.flush()
```

## What you get

cetra reads the spans of the OpenInference Bedrock instrumentation with the OpenInference convention. See [Semantic conventions](https://cetra.sh/docs/concepts/conventions/).

## Limits

- No captured spans of this instrumentation are in the repository. The only check is an SDK test that imports the instrumentor when the extra is installed. This page does not say which Bedrock operations are traced or which fields they fill.
- If a field is missing, look at the span's **Attributes** tab and compare it with [Span attributes](https://cetra.sh/docs/reference/attributes/).

## Next

- [Traces](https://cetra.sh/docs/tracing/traces/): read the trace as a tree and a timeline.
- [Cost and tokens](https://cetra.sh/docs/tracing/cost-and-tokens/): how cost is computed from the token counts.
- [Online rules](https://cetra.sh/docs/evaluation/online-rules/): score these traces automatically.
