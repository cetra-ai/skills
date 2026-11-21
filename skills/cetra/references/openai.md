<!-- Generated from website/src/content/docs/docs/integrations/openai.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# OpenAI

Trace OpenAI calls with cetra: chat completions, the Responses API and embeddings, from Python and TypeScript.

cetra traces OpenAI chat completions, Responses API calls and embeddings, with their messages, tool calls, token counts and cost.

## Install

**Python**

```bash
pip install "cetra-sdk[openai]" openai
```

**TypeScript**

```bash
npm install @cetra-ai/sdk @arizeai/openinference-instrumentation-openai openai
```

## Set up

Turn on the instrumentation when you start cetra, then call OpenAI as usual. The spans nest under your own spans and carry the session, user, tags and metadata of the current context.

**Python**

```python
import cetra
from openai import OpenAI

cetra.init(instrument=["openai"])

client = OpenAI()
client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[{"role": "user", "content": "What is the weather in Paris?"}],
)

cetra.flush()
```

**TypeScript**

```ts
import { init } from "@cetra-ai/sdk";
import { openai } from "@cetra-ai/sdk/openai";
import OpenAI from "openai";
```

```ts
init({ apiKey: process.env.CETRA_API_KEY, instrument: [openai(OpenAI)] });
```

```ts
const client = new OpenAI();
await client.chat.completions.create({
  model: "gpt-4o-mini",
  messages: [{ role: "user", content: "What is the weather in Paris?" }],
});
```

## What you get

- Each chat completion and each Responses API call is an `llm` span with the provider `openai`, the input and output as chat messages, and the tool definitions you passed.
- A tool call in the answer shows as a tool call in the output messages. The tool result you send back shows in the next call's input.
- Token counts include the cached input tokens when OpenAI reports them. cetra computes the cost from its price table.
- Streamed answers record the time to first token with the OpenInference and OpenLLMetry instrumentations. The streamed fixture of the official OpenTelemetry instrumentation has no time to first token.
- An embeddings call is an `embedding` span with the input token count and a cost.

## Limits

- The fixtures were captured with the OpenAI Python SDK 3.45.0 and these instrumentations: OpenInference 0.1.69, OpenLLMetry 0.62.4 and the official `opentelemetry-instrumentation-openai-v2` 2.4b0. Other versions are not covered.
- Embedding vectors are not stored. Pass `capture_content=False` (Python) or `captureContent: false` to the helper and to `init` (TypeScript) to keep prompts and answers out of cetra.
- If you use another instrumentation library, see [OpenInference](https://cetra.sh/docs/integrations/openinference/), [OpenLLMetry](https://cetra.sh/docs/integrations/openllmetry/) or [OpenTelemetry GenAI](https://cetra.sh/docs/integrations/opentelemetry-genai/).

## Next

- [Traces](https://cetra.sh/docs/tracing/traces/): read the trace as a tree and a timeline.
- [Cost and tokens](https://cetra.sh/docs/tracing/cost-and-tokens/): how cost is computed from the token counts.
- [Online rules](https://cetra.sh/docs/evaluation/online-rules/): score these traces automatically.
