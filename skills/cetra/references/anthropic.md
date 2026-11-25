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

## Claude Managed Agents

Claude Managed Agents is Anthropic's hosted agent product, in beta: a session runs in a sandbox hosted by Anthropic (or self-hosted), and the model calls are made from Anthropic's servers, not from your app. The session spans are exported by the Claude Platform from Anthropic's servers to an OpenTelemetry destination an admin sets; your app sends nothing for them. Anthropic's documentation names the session spans `anthropic.session.turn`, `anthropic.model_request` and `anthropic.tool_use`, among others. This route is setup only: it was not run against cetra, because a session needs Anthropic's real Sessions API.

Adding a destination needs the Console admin role, or the Admin API (`POST /v1/organizations/telemetry_destinations`). Point it at cetra as a "Custom" destination: the endpoint is a hostname on port 443, not an IP address, and Anthropic's API appends `/v1/traces` itself and sends gzip-compressed protobuf, which cetra accepts. Add a header `Authorization: Bearer sk-cetra-…` with your cetra API key, and set the destination's content mode to full content if you want prompts, messages and tool arguments recorded; the default, `metadata_only`, records no content. [Send OpenTelemetry](https://cetra.sh/docs/get-started/opentelemetry/) describes cetra's side of the endpoint.

Because the spans leave from Anthropic's servers, cetra's endpoint must be reachable from the public internet: the hostname must resolve to a public IP. It must answer within 5 s for TLS and headers, and within 10 s in total. A `3xx` response disables the destination, and so do 24 hours of failed deliveries.

## Limits

- The fixtures were captured with the Anthropic Python SDK 2.4.0 and these instrumentations: OpenInference 3.3.0 and OpenLLMetry 0.62.4. Other versions are not covered.
- Anthropic's own SDKs start a span of their own for each call, `anthropic.messages.create`, on the global tracer provider: Python `anthropic` 2.8.0 and TypeScript `@anthropic-ai/sdk` 1.8.0, the versions tested, both do. With the setups on this page each call is still recorded once: OpenInference's Anthropic instrumentor (Python 3.3.0 or later, TypeScript 1.0.1 or later) keeps the SDK's span out of the calls it traces. Without `use_global_provider=True` (TypeScript `useGlobalProvider: true`) the SDK's spans do not reach cetra at all.
- OpenLLMetry's Anthropic instrumentation (0.62.4) does not keep the SDK's span out: its `anthropic.chat` span and the SDK's span both carry the call's tokens, which then count twice. With OpenLLMetry, set `ANTHROPIC_OPEN_TELEMETRY=false`, which both SDKs read, to turn the SDK's own spans off.
- With OpenLLMetry's Anthropic instrumentation the prompt token count can exclude the cache. cetra adds the cache back, so the stored counts follow one rule.
- The TypeScript helper is covered by an SDK test, not by captured spans.
- The Claude Managed Agents route is setup only: no session was run against cetra, the span shapes come from Anthropic's documentation, and the beta means the span names and attributes can change.
- A Claude Platform destination sends prompts, messages and tool arguments only in full content mode; the default is `metadata_only`. An organization can have up to 10 destinations, with 20 headers each.

## Next

- [Traces](https://cetra.sh/docs/tracing/traces/): read the trace as a tree and a timeline.
- [Cost and tokens](https://cetra.sh/docs/tracing/cost-and-tokens/): how cost is computed from the token counts.
- [Online rules](https://cetra.sh/docs/evaluation/online-rules/): score these traces automatically.
