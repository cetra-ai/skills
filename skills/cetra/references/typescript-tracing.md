<!-- Generated from website/src/content/docs/docs/reference/typescript/tracing.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# Tracing (TypeScript)

Trace functions with observe, write spans by hand with withSpan and record LLM calls with withLlmSpan.

Use `observe`, `withSpan` and `withLlmSpan` to turn your TypeScript code into spans.

## Wrapper rules

`observe(fn, options?)` wraps a function so that each call is one span.

- **Input**: no arguments record none; one argument records that value; more record the JSON array of them. Destructuring defaults are invisible to `observe`: pass the fields you want recorded explicitly.
- **Output**: the return value, or the resolved value of a returned promise. `undefined` records no output; return `null` to record a JSON `null`.
- **Generators**: a generator or async generator function (detected at wrap time) is traced as a generator: the span starts at the first `next()`, the output is the yielded items, their concatenation when every item is a string, else the JSON array. An early `return()` ends the span with status ok, not an error.
- **Errors**: a throw records one `exception` event and sets the status to error, then re-throws unchanged. Any other function shape, including one _returning_ a generator, is traced as a call, and a returned generator object is passed through untouched, never consumed.
- `captureInput` / `captureOutput` (default `true`) turn each side off.
- An invalid `kind` throws a `TypeError` at wrap time, before the function runs.

## Manual spans and `withLlmSpan`

`withSpan(name, options?, fn)` runs `fn` inside a span; the body's return value is never recorded. Only `span.setInput`, `span.setOutput` and `span.setMetadata` write attributes.

```ts
withLlmSpan(
  "chat",
  {
    provider: "openai",
    model: "gpt-4o-mini",
    inputMessages: [{ role: "user", content: "hi" }],
    params: { temperature: 0.2 },
  },
  (span) => {
    span.setOutputMessages([{ role: "assistant", content: "hello" }]);
    span.setUsage({ inputTokens: 12, outputTokens: 3 });
  },
);
```

`withLlmSpan` writes the GenAI attributes (`gen_ai.*`), `cetra.span.kind = "llm"` and, through the handle, the response model, usage, cost (`setCost(totalUsd)`) and time-to-first-token (`recordFirstToken()`). Messages may be the canonical `{ role, parts }` format or the short `{ role, content: "<text>" }` form, which becomes one text part.
