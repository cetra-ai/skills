<!-- Generated from website/src/content/docs/docs/integrations/anthropic.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# Anthropic

Trace Anthropic calls with cetra: messages, streaming, tool use, thinking blocks and cache tokens, from Python and TypeScript.

cetra traces Anthropic messages, including streamed answers, tool use, thinking blocks and prompt caching.

## Install

**Python**

```bash
pip install "cetra-sdk[anthropic]" anthropic
```

**TypeScript**

```bash
npm install @cetra-ai/sdk @arizeai/openinference-instrumentation-anthropic @anthropic-ai/sdk
```

## Set up

**Python**

```python
import cetra
from anthropic import Anthropic

cetra.init(instrument=["anthropic"])

client = Anthropic()
client.messages.create(
    model="claude-sonnet-4-5",
    max_tokens=256,
    messages=[{"role": "user", "content": "What is the weather in Paris?"}],
)

cetra.flush()
```

**TypeScript**

```ts
import { init } from "@cetra-ai/sdk";
import { anthropic } from "@cetra-ai/sdk/anthropic";
import Anthropic from "@anthropic-ai/sdk";
```

```ts
init({ apiKey: process.env.CETRA_API_KEY, instrument: [anthropic(Anthropic)] });
```

```ts
const client = new Anthropic();
await client.messages.create({
  model: "claude-sonnet-4-5",
  max_tokens: 256,
  messages: [{ role: "user", content: "What is the weather in Paris?" }],
});
```

## What you get

- Each message is an `llm` span with the provider `anthropic`, the input and output as chat messages, and the tools you passed.
- Streamed answers with a tool call are captured as one span with the text and the tool call.
- Thinking blocks appear in the output.
- Cache read and cache write tokens are recorded. The input token count includes them, and cetra prices them separately.
- The cost is computed from cetra's price table.

## Limits

- The fixtures were captured with the Anthropic Python SDK 2.4.0 and these instrumentations: OpenInference 3.3.0 and OpenLLMetry 0.62.4. Other versions are not covered.
- With OpenLLMetry's Anthropic instrumentation the prompt token count can exclude the cache. cetra adds the cache back, so the stored counts follow one rule.
- The TypeScript helper is covered by an SDK test, not by captured spans.

## Next

- [Traces](https://cetra.sh/docs/tracing/traces/): read the trace as a tree and a timeline.
- [Cost and tokens](https://cetra.sh/docs/tracing/cost-and-tokens/): how cost is computed from the token counts.
- [Online rules](https://cetra.sh/docs/evaluation/online-rules/): score these traces automatically.
