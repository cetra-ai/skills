<!-- Generated from website/src/content/docs/docs/reference/typescript/opentelemetry.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# OpenTelemetry (TypeScript)

Enable instrumentation for OpenAI, Anthropic and LangChain, use the Vercel AI SDK, work next to an existing OpenTelemetry setup and mask payloads.

Use these helpers to capture spans from LLM libraries, to share a process with other OpenTelemetry code and to keep content out of your traces.

## Instrumentation (OpenAI, Anthropic, LangChain)

`init({ instrument: [...] })` takes instrumentation objects (anything with `setTracerProvider`, `enable` and `disable`). The subpath helpers build the OpenInference instrumentation and patch the module by hand, so ESM works without loader flags:

```ts
import { init } from "@cetra-ai/sdk";
import { openai } from "@cetra-ai/sdk/openai";
import OpenAI from "openai";

init({ apiKey: process.env.CETRA_API_KEY, instrument: [openai(OpenAI)] });
```

The same shape exists as `@cetra-ai/sdk/anthropic` (takes the `Anthropic` class from `@anthropic-ai/sdk`) and `@cetra-ai/sdk/langchain` (takes the LangChain global from `@langchain/core`). The OpenInference packages are optional peers. Pass the helpers' `captureContent` the same value as `init`'s.

## Vercel AI SDK

The AI SDK sends spans to the tracer it is given; `aiTelemetry()` hands out one from the cetra provider (scope `ai`) plus your context values as telemetry metadata.

```ts
// Pass `telemetry` as `experimental_telemetry` to the Vercel AI SDK; the
// tracer it hands out is the cetra one, so AI spans land in cetra.
const telemetry = aiTelemetry({ sessionId: "chat-42", functionId: "chat" });
telemetry.tracer?.startSpan("ai.chat").end();
```

Full usage with the AI SDK itself:

```ts
import { aiTelemetry } from "@cetra-ai/sdk";
import { generateText } from "ai";

const result = await generateText({
  model: myModel,
  experimental_telemetry: aiTelemetry({ sessionId: "chat-42", functionId: "chat" }),
});
```

## Existing OpenTelemetry setups

By default the SDK keeps its own `TracerProvider` and never registers it globally, so it cannot hijack an app's OpenTelemetry setup. The OpenTelemetry _context_ is always the global one, so instrumentor spans nest under ours.

- `useGlobalProvider: true` registers our provider as the global one when none is set; OpenTelemetry JS 2.x providers cannot take processors after construction, so with an app provider already registered the SDK logs one warning and stays private.
- `spanProcessor()` returns our span processor, for apps that build their own OpenTelemetry pipeline and add it to their provider.
- `exportFilter(span)` replaces the default export rule (which, in global mode, keeps spans whose scope is `cetra` or `ai` or that carry known LLM attribute prefixes).
- `tracerProvider()` returns the provider in use.

## Masking and `captureContent`

`mask(value)` runs on every value the SDK records (inputs, outputs, metadata, LLM messages) before serialization, and at export time on payload keys of other spans (instrumentor spans included). It gets the decoded value and must return a new one; a string stays a string, anything else is serialized back. If it throws, the value becomes `"[cetra: mask failed]"` (fail closed).

`captureContent: false` records no payload attributes on our spans and deletes the payload keys from other spans; token counts, models and costs still flow.

```ts
import { init } from "@cetra-ai/sdk";

init({
  apiKey: process.env.CETRA_API_KEY,
  mask: (value) => (typeof value === "string" ? value.replaceAll("sk-live-", "[key]") : value),
});
```
