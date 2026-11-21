<!-- Generated from website/src/content/docs/docs/integrations/index.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# Integrations

The frameworks, providers and instrumentation libraries cetra works with, how each is connected and what it is tested against.

Use this page to find how to connect your framework, provider or instrumentation library to cetra.

| Integration | Python | TypeScript | How | Tested against |
|---|---|---|---|---|
| [OpenAI](https://cetra.sh/docs/integrations/openai/) | ✓ | ✓ | `instrument=["openai"]` or `openai(OpenAI)` | captured |
| [Anthropic](https://cetra.sh/docs/integrations/anthropic/) | ✓ | ✓ | `instrument=["anthropic"]` or `anthropic(Anthropic)` | captured |
| [Google Gen AI](https://cetra.sh/docs/integrations/google-genai/) | ✓ | — | `instrument=["google-genai"]` | captured |
| [Amazon Bedrock](https://cetra.sh/docs/integrations/bedrock/) | ✓ | — | `instrument=["bedrock"]` | SDK test |
| [LiteLLM](https://cetra.sh/docs/integrations/litellm/) | ✓ | — | `instrument=["litellm"]` | SDK test |
| [LangChain and LangGraph](https://cetra.sh/docs/integrations/langchain/) | ✓ | ✓ | `instrument=["langchain"]` or `langchain(…)` | captured (Python), SDK test (TypeScript) |
| [LlamaIndex](https://cetra.sh/docs/integrations/llama-index/) | ✓ | — | `instrument=["llama-index"]` | SDK test |
| [Vercel AI SDK](https://cetra.sh/docs/integrations/vercel-ai-sdk/) | — | ✓ | `aiTelemetry()` | captured |
| [OpenAI Agents SDK](https://cetra.sh/docs/integrations/openai-agents/) | ✓ | — | `instrument=["openai-agents"]` | captured |
| [CrewAI](https://cetra.sh/docs/integrations/crewai/) | ✓ | — | `instrument=["crewai"]` | SDK test |
| [DSPy](https://cetra.sh/docs/integrations/dspy/) | ✓ | — | `instrument=["dspy"]` | SDK test |
| [Google ADK](https://cetra.sh/docs/integrations/google-adk/) | ✓ | — | ADK's own OpenTelemetry spans | captured |
| [OpenInference](https://cetra.sh/docs/integrations/openinference/) | ✓ | ✓ | point its exporter at cetra | captured |
| [OpenLLMetry (Traceloop)](https://cetra.sh/docs/integrations/openllmetry/) | ✓ | ✓ | point its exporter at cetra | captured |
| [OpenTelemetry GenAI](https://cetra.sh/docs/integrations/opentelemetry-genai/) | ✓ | ✓ | the official `opentelemetry-instrumentation-*` packages | captured |
| [From Langfuse](https://cetra.sh/docs/integrations/from-langfuse/) | ✓ | — | keep the Langfuse SDK, export its spans to cetra | captured |
| [From Laminar](https://cetra.sh/docs/integrations/from-laminar/) | ✓ | — | keep the Laminar SDK, export its spans to cetra | captured |
| [Claude Code](https://cetra.sh/docs/integrations/claude-code/) | — | — | `cetra agents setup claude-code` | captured |
| [Codex CLI](https://cetra.sh/docs/integrations/codex/) | — | — | `cetra agents setup codex` | captured |
| [Gemini CLI](https://cetra.sh/docs/integrations/gemini-cli/) | — | — | `cetra agents setup gemini-cli` | captured |
| [GitHub Copilot](https://cetra.sh/docs/integrations/copilot/) | — | — | `cetra agents setup copilot` | hand-written |

"Captured" means the repository holds spans recorded from the real library, and a test checks how cetra reads them. "SDK test" means an SDK test checks the helper, and the repository holds no recorded spans for it. A ✓ for TypeScript means a helper in `@cetra-ai/sdk`, or a TypeScript library whose spans are captured. A — means no helper or captured run exists for that language: you can still use the library's own OpenTelemetry instrumentation with [Send OpenTelemetry](https://cetra.sh/docs/get-started/opentelemetry/).

## Coding agents

- [Claude Code](https://cetra.sh/docs/integrations/claude-code/): Prompts, model calls and tools, with the Claude Agent SDK.
- [Codex CLI](https://cetra.sh/docs/integrations/codex/): Turns and tool calls from codex exec.
- [Gemini CLI](https://cetra.sh/docs/integrations/gemini-cli/): Model calls and tool batches.
- [GitHub Copilot](https://cetra.sh/docs/integrations/copilot/): Copilot CLI and VS Code Copilot Chat.

## Providers

- [OpenAI](https://cetra.sh/docs/integrations/openai/): Chat, Responses API and embeddings.
- [Anthropic](https://cetra.sh/docs/integrations/anthropic/): Messages, streaming, thinking and cache tokens.
- [Google Gen AI](https://cetra.sh/docs/integrations/google-genai/): Gemini through the Google Gen AI SDK.
- [Amazon Bedrock](https://cetra.sh/docs/integrations/bedrock/): Bedrock calls from Python.
- [LiteLLM](https://cetra.sh/docs/integrations/litellm/): LiteLLM calls from Python.

## Frameworks

- [LangChain and LangGraph](https://cetra.sh/docs/integrations/langchain/): Chains, agents, models and tools.
- [LlamaIndex](https://cetra.sh/docs/integrations/llama-index/): LlamaIndex from Python.
- [Vercel AI SDK](https://cetra.sh/docs/integrations/vercel-ai-sdk/): Versions 4 to 7.
- [OpenAI Agents SDK](https://cetra.sh/docs/integrations/openai-agents/): Agent runs and function tools.
- [CrewAI](https://cetra.sh/docs/integrations/crewai/): CrewAI from Python.
- [DSPy](https://cetra.sh/docs/integrations/dspy/): DSPy from Python.
- [Google ADK](https://cetra.sh/docs/integrations/google-adk/): Agent Development Kit runs.

## Instrumentation libraries

- [OpenInference](https://cetra.sh/docs/integrations/openinference/): Arize's instrumentation, with any exporter.
- [OpenLLMetry](https://cetra.sh/docs/integrations/openllmetry/): Traceloop's instrumentation and SDK.
- [OpenTelemetry GenAI](https://cetra.sh/docs/integrations/opentelemetry-genai/): The official GenAI instrumentation packages.

## Migrating

- [From Langfuse](https://cetra.sh/docs/integrations/from-langfuse/): Keep the Langfuse SDK and send its spans to cetra.
- [From Laminar](https://cetra.sh/docs/integrations/from-laminar/): Keep the Laminar SDK and send its spans to cetra.

Anything else that speaks OpenTelemetry works through [Send OpenTelemetry](https://cetra.sh/docs/get-started/opentelemetry/).
