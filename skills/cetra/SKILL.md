---
name: cetra
description: Add LLM tracing with cetra to an app, with the cetra Python or TypeScript SDK or plain OpenTelemetry, then check that the traces arrive. Use when the user asks to add cetra, LLM observability, or tracing of LLM calls, agents, tools or prompts, or to send OpenTelemetry GenAI spans to cetra.
license: MIT
allowed-tools: Bash(npx @cetra-ai/cli query:*) Bash(cetra query:*) Bash(cetra traces:*) Bash(cetra projects:*)
metadata:
  homepage: https://cetra.sh
---

# cetra

cetra is an open-source LLM observability platform. An app sends OpenTelemetry spans to cetra, and cetra shows them as traces with their prompts, answers, tokens and cost. The cetra SDKs for Python and TypeScript set this up in a few lines, and plain OpenTelemetry works from any language.

Follow the steps in order. Keep the change small: add tracing, change nothing else in the app.

## 1. Check the key and the URL

The app needs two environment variables:

- `CETRA_API_KEY`: a project key that starts with `sk-cetra-`.
- `CETRA_BASE_URL`: the cetra server, for example `https://eu1.cetra.sh` or `http://localhost:8080`.

`npx @cetra-ai/cli init` writes both to `.env`. If they are missing, ask the user to run it. Never ask the user to paste a key into the chat, never write a key into code, and never commit `.env`.

The SDKs read the process environment, not the `.env` file. Load `.env` the way the app already loads its settings: `python-dotenv`, `node --env-file=.env`, or the framework's own loader. If the app loads nothing yet, use one of those.

## 2. Find the stack

Read the project's manifest (`pyproject.toml`, `requirements.txt`, `package.json`) and the code that calls the model. Then read the matching reference pages:

| The app uses | Read |
|---|---|
| Python | [python.md](references/python.md), [python-tracing.md](references/python-tracing.md) |
| TypeScript or JavaScript | [typescript.md](references/typescript.md), [typescript-tracing.md](references/typescript-tracing.md) |
| A serverless or edge runtime | [typescript-runtimes.md](references/typescript-runtimes.md), [python-opentelemetry.md](references/python-opentelemetry.md) |
| Another language, or its own OpenTelemetry setup | [opentelemetry.md](references/opentelemetry.md), [otlp.md](references/otlp.md), [python-opentelemetry.md](references/python-opentelemetry.md), [typescript-opentelemetry.md](references/typescript-opentelemetry.md) |
| OpenAI | [openai.md](references/openai.md) |
| Anthropic | [anthropic.md](references/anthropic.md) |
| Amazon Bedrock | [bedrock.md](references/bedrock.md) |
| Google Gen AI | [google-genai.md](references/google-genai.md) |
| Google ADK | [google-adk.md](references/google-adk.md) |
| LangChain or LangGraph | [langchain.md](references/langchain.md) |
| LlamaIndex | [llama-index.md](references/llama-index.md) |
| LiteLLM | [litellm.md](references/litellm.md) |
| OpenAI Agents SDK | [openai-agents.md](references/openai-agents.md) |
| CrewAI | [crewai.md](references/crewai.md) |
| DSPy | [dspy.md](references/dspy.md) |
| Vercel AI SDK | [vercel-ai-sdk.md](references/vercel-ai-sdk.md) |
| OpenInference instrumentation | [openinference.md](references/openinference.md) |
| OpenLLMetry (Traceloop) | [openllmetry.md](references/openllmetry.md) |
| OpenTelemetry GenAI instrumentation | [opentelemetry-genai.md](references/opentelemetry-genai.md) |
| Langfuse today | [from-langfuse.md](references/from-langfuse.md) |
| Laminar today | [from-laminar.md](references/from-laminar.md) |

[integrations.md](references/integrations.md) lists every integration. A library that is not listed is still traced when it calls a listed provider.

## 3. Add tracing

- Install the SDK with the project's package manager: `cetra-sdk` with the extras of the libraries the app uses (for example `cetra-sdk[openai]`), or `@cetra-ai/sdk` with the instrumentation packages its reference page names.
- Call `init` once, at startup, before the app creates its LLM clients. Pass the instrumentations the reference page gives for each library. Leave `api_key` and `base_url` out: the SDK reads `CETRA_API_KEY` and `CETRA_BASE_URL`.
- Wrap the app's entry point or request handler with `observe`, so each request is one trace with the LLM calls under it.
- When the app has conversations or signed-in users, set `session_id` and `user_id` with `context` (Python) or `withContext` (TypeScript). See [sessions.md](references/sessions.md) and [users.md](references/users.md).
- In scripts, tests and serverless handlers, call `flush()` (Python) or `await flush()` (TypeScript) before the process ends. Otherwise the last spans are lost.
- If the app already has its own OpenTelemetry setup, keep it, and read the OpenTelemetry page of its language before changing anything.
- To leave prompts and answers out of the traces, set `CETRA_CAPTURE_CONTENT=false` (or `capture_content=False` / `captureContent: false` in `init`); see [privacy-controls.md](references/privacy-controls.md).

## 4. Run the app

Run the app or its tests once, so that it makes at least one LLM call. This needs the provider's own key, for example `OPENAI_API_KEY`. If you cannot make a real call, say so and stop here: the user runs it.

## 5. Check the traces

Run:

```bash
npx @cetra-ai/cli query "SELECT trace_id, name, start_time, span_count, llm_count, error_count FROM traces ORDER BY start_time DESC LIMIT 5" --since 15m --json
```

The command reads the server and the project from `.cetra/project.json`, which `npx @cetra-ai/cli init` wrote. The columns are in [sql-schema.md](references/sql-schema.md).

- **Rows come back:** check that `llm_count` is above 0 and `error_count` is 0. To see the spans of a trace, query the `spans` view with `WHERE trace_id = '<id>'`.
- **No rows:** read "No traces at all" and "A short script loses its last spans" in [troubleshooting.md](references/troubleshooting.md). The usual causes are a missing `flush()`, a key or URL the app did not load, or `init` called after the LLM client was created.

If the user has the `cetra` binary and has run `cetra login`, `cetra query "<sql>" --project <org>/<project> --since 15m --format json` works too. An agent connected to cetra's MCP server can run the same SQL with its `query_sql` tool.

## 6. Report

Tell the user which files changed, which libraries are traced, and where to see the traces: `<CETRA_BASE_URL>/<org>/<project>/traces`.

## More

- The whole documentation for agents: `https://cetra.sh/llms.txt`.
- Search the docs with the docs MCP server at `https://cetra.sh/mcp`.
- Span attributes cetra reads, for hand-made spans: [attributes.md](references/attributes.md) and [metadata-and-tags.md](references/metadata-and-tags.md).
- Tracing the coding agent itself (Claude Code, Codex, Gemini CLI, Copilot, Qwen Code, Pi, OpenClaw, OpenCode, Goose, Oh My Pi) is a different setup, `cetra agents setup`: see [Claude Code](https://cetra.sh/docs/integrations/claude-code/).
