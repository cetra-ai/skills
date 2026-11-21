<!-- Generated from website/src/content/docs/docs/reference/typescript/index.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# TypeScript SDK

Install the cetra TypeScript SDK, configure it and find your way around its reference pages.

Use the TypeScript SDK to send traces and scores from Node, Bun, Deno or an edge runtime to cetra. The `@cetra-ai/sdk` package (MIT, ESM only) is a thin layer over OpenTelemetry: tracing goes out as OTLP/HTTP protobuf, and scores and feedback go over a small REST surface. It runs on Node 22.12 or later, Bun, Deno and edge runtimes, with one `fetch`-based transport everywhere.

The pages of this reference:

- [Tracing](https://cetra.sh/docs/reference/typescript/tracing/): `observe`, `withSpan` and `withLlmSpan`.
- [Context](https://cetra.sh/docs/reference/typescript/context/): sessions, users, tags and metadata, and baggage.
- [Scores, datasets and experiments](https://cetra.sh/docs/reference/typescript/scores-datasets-experiments/): `score`, the `Client` and `evaluate`.
- [OpenTelemetry](https://cetra.sh/docs/reference/typescript/opentelemetry/): instrumentation helpers, the Vercel AI SDK, existing OpenTelemetry setups and masking.
- [Runtimes](https://cetra.sh/docs/reference/typescript/runtimes/): edge runtimes, serverless, browser feedback and known limits.
- [API](https://cetra.sh/docs/reference/typescript/api/): every export.

## Install

```sh
pnpm add @cetra-ai/sdk
```

The instrumentation helpers need their OpenInference packages (optional peers, installed next to the SDK):

```sh
pnpm add @arizeai/openinference-instrumentation-openai openai
```

## Initialize

For a first trace, follow the [quick start](https://cetra.sh/docs/get-started/quickstart/).

`init()` is once per process: a second call logs one warning and changes nothing. Without an API key (argument and `CETRA_API_KEY` both unset) it warns once and runs disabled; `CETRA_ENABLED=false` silences the warning.

## Configuration

| Option              | Env                     | Default                           |
| ------------------- | ----------------------- | --------------------------------- |
| `apiKey`            | `CETRA_API_KEY`         | unset: one warning, SDK disabled  |
| `baseUrl`           | `CETRA_BASE_URL`        | `http://localhost:8080`           |
| `environment`       | `CETRA_ENVIRONMENT`     | unset                             |
| `release`           | `CETRA_RELEASE`         | unset                             |
| `captureContent`    | `CETRA_CAPTURE_CONTENT` | `true`                            |
| `sampleRate`        | `CETRA_SAMPLE_RATE`     | `1` (trace-id ratio sampler)      |
| `useGlobalProvider` | —                       | `false`                           |
| `instrument`        | —                       | `[]`                              |
| `mask`              | —                       | none                              |
| `exportFilter`      | —                       | none                              |
| `enabled`           | `CETRA_ENABLED`         | `true` (no-op mode when `false`)  |

An explicit argument wins over the env var, which wins over the default. Env values are trimmed; an empty value counts as unset. An invalid value throws a `ConfigError` naming the option, never its value. `OTEL_SERVICE_NAME` (from the config's env source) sets `service.name`.

## What is never logged

Logs and warnings never contain payloads (prompts, completions, attribute values), API keys or email addresses: ids, sizes and counts only. An invalid score logs `cetra: score not queued: <reason>` with the reason, never the value. The API key never appears in errors or serialized config.
