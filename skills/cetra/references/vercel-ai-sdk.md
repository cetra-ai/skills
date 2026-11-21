<!-- Generated from website/src/content/docs/docs/integrations/vercel-ai-sdk.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# Vercel AI SDK

Trace the Vercel AI SDK with cetra: generateText, streamText, tool calls, structured output and embeddings, versions 4 to 7.

cetra traces the Vercel AI SDK's generateText, streamText, generateObject, tool calls and embeddings, with sessions, users and metadata.

## Install

```bash
npm install @cetra-ai/sdk ai @ai-sdk/openai
```

## Set up (AI SDK 4 to 6)

The AI SDK sends spans to the tracer you give it. `aiTelemetry()` hands out a tracer from cetra, and adds your session, user, tags and metadata to the telemetry metadata.

```ts
import { aiTelemetry, init } from "@cetra-ai/sdk";
import { generateText } from "ai";
import { openai } from "@ai-sdk/openai";
```

```ts
// Pass `telemetry` as `experimental_telemetry` to the Vercel AI SDK; the
// tracer it hands out is the cetra one, so AI spans land in cetra.
const telemetry = aiTelemetry({ sessionId: "chat-42", functionId: "chat" });
telemetry.tracer?.startSpan("ai.chat").end();
```

```ts
init({ apiKey: process.env.CETRA_API_KEY });

const result = await generateText({
  model: openai("gpt-4o-mini"),
  prompt: "What is the weather in Paris?",
  experimental_telemetry: aiTelemetry({ sessionId: "chat-42", functionId: "chat" }),
});
```

## Set up (AI SDK 7)

AI SDK 7 does not emit spans by itself. The spans come from the `@ai-sdk/otel` package, passed in `telemetry.integrations`, and go to the global OpenTelemetry tracer provider. The fixtures for version 7 were captured this way, with a global provider. To use it with cetra, register cetra's provider as the global one with `useGlobalProvider: true`.

```ts
import { init } from "@cetra-ai/sdk";
import { OpenTelemetry } from "@ai-sdk/otel";
import { openai } from "@ai-sdk/openai";
import { generateText } from "ai";

init({ apiKey: process.env.CETRA_API_KEY, useGlobalProvider: true });

await generateText({
  model: openai("gpt-4o-mini"),
  prompt: "What is the weather in Paris?",
  telemetry: { isEnabled: true, integrations: new OpenTelemetry() },
});
```

## What you get

- The outer call (`ai.generateText`) is a `chain` span and each model call under it (`doGenerate` or `doStream`) is an `llm` span. Tokens and cost are on the `llm` spans only, so they are never counted twice.
- A tool call is a `tool` span with its arguments and result.
- Structured output (`generateObject`) is stored as JSON.
- `streamText` records the time to first token.
- Cache read and cache write tokens are recorded for Anthropic models. cetra adjusts the input count where the AI SDK reports it without the cache.
- `embed` and `embedMany` are traced.
- Session ID, user ID and metadata set through the telemetry metadata arrive as the span's session, user and metadata.
- In version 7 the spans are named `invoke_agent …`, `step …` and `chat …`.

## Limits

- Versions 5, 6 and 7 are covered by captured spans. Version 4 is covered by a hand-written case that checks the older `args` and `result` shapes.
- Images in the prompt, passed as base64, are extracted as media.
- With `useGlobalProvider`, cetra keeps only LLM-related spans from your other OpenTelemetry code. See [OpenTelemetry (TypeScript)](https://cetra.sh/docs/reference/typescript/opentelemetry/).

## Next

- [Traces](https://cetra.sh/docs/tracing/traces/): read the trace as a tree and a timeline.
- [Cost and tokens](https://cetra.sh/docs/tracing/cost-and-tokens/): how cost is computed from the token counts.
- [Online rules](https://cetra.sh/docs/evaluation/online-rules/): score these traces automatically.
