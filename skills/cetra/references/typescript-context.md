<!-- Generated from website/src/content/docs/docs/reference/typescript/context.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# Context (TypeScript)

Attach sessions, users, tags and metadata to every span and send them downstream as baggage.

Use `withContext` to group traces by session and user.

## Context and baggage

```ts
const turn = observe(({ question }: { question: string }) => `answered: ${question}`, {
  name: "turn",
});
withContext({ sessionId: "chat-42", userId: "u-17", tags: ["beta"] }, () =>
  turn({ question: "why?" }),
);
```

The values live in the OpenTelemetry context, so they follow `AsyncLocalStorage` into promises and concurrent flows; nested blocks merge (inner session and user win, tags union, metadata merges by key). They are stamped on every span of the SDK's provider: session and user ids, tags, metadata, `environment` and `release`. Metadata is recorded to be read back by the server: keep personal data out of it, or mask it (see [Masking](https://cetra.sh/docs/reference/typescript/opentelemetry/#masking-and-capturecontent)). `propagate: true` also sets the session and user ids as W3C baggage entries for downstream services.

`prompt` links the spans to a prompt from `getPrompt()`: `withContext({ prompt }, fn)` stamps `cetra.prompt.name` and `cetra.prompt.version` on every span inside the block, and an inner prompt replaces an outer one. A fallback prompt has no version, so only its name is stamped. Any value that is not a `Prompt` is ignored. The [Prompts](https://cetra.sh/docs/evaluation/prompts/) page shows the whole flow.
