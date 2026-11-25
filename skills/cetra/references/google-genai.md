<!-- Generated from website/src/content/docs/docs/integrations/google-genai.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# Google Gen AI

Trace Google Gen AI calls with cetra: Gemini generateContent, streaming, multimodal input and reasoning tokens, from Python.

cetra traces calls to Gemini through the Google Gen AI SDK, including streamed answers, images in the input and reasoning tokens. For Vertex AI, see [Vertex AI](https://cetra.sh/docs/integrations/vertex-ai/).

## Install

```bash
pip install "cetra-sdk[google-genai]" google-genai
```

## Set up

```python
import cetra
from google import genai

cetra.init(instrument=["google-genai"])

client = genai.Client()
client.models.generate_content(
    model="gemini-2.5-flash",
    contents="What is the weather in Paris?",
)

cetra.flush()
```

## What you get

- Each `generateContent` call is an `llm` span with the input and output as chat messages.
- A streamed call is captured as one `llm` span (OpenInference names it `GenerateContentStream`).
- Reasoning tokens are recorded when Gemini reports them.
- An image in the input is stored as media and replaced by a reference, as for every other provider. See [Media](https://cetra.sh/docs/tracing/media/).
- Cache read tokens are recorded when Gemini reports them.

## Limits

- The fixtures were captured with `google-genai` 2.34.0 and these instrumentations: OpenInference 1.5.0, the official OpenTelemetry `opentelemetry-instrumentation-google-genai` 1.1b1 and OpenLLMetry 0.62.4.
- The provider name depends on the instrumentation: `gcp.gen_ai` from OpenInference and `gcp.gemini` from the official OpenTelemetry one. cetra prices both from the same Gemini prices.
- The `instrument` name is checked by an SDK test that imports the instrumentor when the extra is installed. cetra-sdk's own spans for this name are not captured.

## Next

- [Traces](https://cetra.sh/docs/tracing/traces/): read the trace as a tree and a timeline.
- [Cost and tokens](https://cetra.sh/docs/tracing/cost-and-tokens/): how cost is computed from the token counts.
- [Online rules](https://cetra.sh/docs/evaluation/online-rules/): score these traces automatically.
