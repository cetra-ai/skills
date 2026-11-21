<!-- Generated from website/src/content/docs/docs/reference/typescript/runtimes.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# Runtimes (TypeScript)

Run the TypeScript SDK on edge runtimes and serverless platforms, send feedback from a browser and know its known limits.

Use this page to run the SDK outside a long-lived Node process.

## Edge runtimes and serverless

One transport everywhere: OTLP protobuf over `fetch`, gzipped through `CompressionStream` when the runtime has it. On Cloudflare Workers, enable Node compatibility (the SDK uses `AsyncLocalStorage` for context):

```toml
compatibility_flags = ["nodejs_compat"]
```

In serverless (Workers, Lambda, edge functions), flush explicitly. The `beforeExit` hook only fires when the event loop empties, never on a frozen or reused invocation:

```ts
import { flush } from "@cetra-ai/sdk";

export default {
  async fetch(request, env, ctx) {
    const response = handle(request);
    ctx.waitUntil(flush());
    return response;
  },
};
```

## Browser feedback

The `@cetra-ai/sdk/browser` subpath imports nothing from OpenTelemetry; it posts an end-user feedback score with a public key (`pk-cetra-…`) and never throws:

```ts
import { feedback } from "@cetra-ai/sdk/browser";

const { ok } = await feedback({
  baseUrl: "https://cetra.example.org",
  publicKey: "pk-cetra-…",
  traceId, // 32 hex chars, from traceparent or your app
  value: 1,
});
```

## Known limits

- **Zero-argument functions and `undefined`**: no arguments record no input (Python records `{}`); an `undefined` return records no output (Python records `null`). Return `null` to record a JSON `null`.
- **Integral floats**: JavaScript has one number type, and OpenTelemetry JS encodes an integral number as an int attribute; Python writes a double for `1.0`. The server reads both. `bigint` becomes a decimal string in payloads.
- **Resource attributes**: only `OTEL_SERVICE_NAME` is read (from the config's env source); `OTEL_RESOURCE_ATTRIBUTES` is not (Python reads it).
- **Usage counts**: `setUsage` records only safe integers (`Number.isSafeInteger`); a larger value, a non-integer or a negative one is skipped with a debug line. Python's `set_usage` records any non-negative `int`, including values beyond 2^53.
- **Abandoned generators**: a half-consumed generator is left unexported (TypeScript has no finalizer); Python's garbage collector ends it with status ok.
- **Cloudflare Workers** need `nodejs_compat` (above); flush through `ctx.waitUntil(flush())` before the invocation is frozen.
- **Serverless**: call `await flush()` (or `shutdown()`) at the end of each invocation; the SDK installs no signal handlers and `beforeExit` does not fire on `process.exit()`.
