<!-- Generated from website/src/content/docs/docs/integrations/index.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# Integrations

The frameworks, providers and instrumentation libraries cetra works with, how each is connected and what it is tested against.

Use this page to find how to connect your framework, provider or instrumentation library to cetra.

## Providers

| Integration | Python | TypeScript | How | Tested against |
|---|---|---|---|---|
| [Amazon Bedrock](https://cetra.sh/docs/integrations/bedrock/) | ✓ | — | `instrument=["bedrock"]` | SDK test |
| [Anthropic](https://cetra.sh/docs/integrations/anthropic/) | ✓ | ✓ | `instrument=["anthropic"]` or `anthropic(Anthropic)` | captured |
| [Azure OpenAI](https://cetra.sh/docs/integrations/azure-openai/) | ✓ | ✓ | `instrument=["openai"]` | captured |
| [Baidu Qianfan](https://cetra.sh/docs/integrations/qianfan/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [Baseten](https://cetra.sh/docs/integrations/baseten/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [BytePlus ModelArk](https://cetra.sh/docs/integrations/byteplus/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [Cerebras](https://cetra.sh/docs/integrations/cerebras/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [Cloudflare Workers AI](https://cetra.sh/docs/integrations/cloudflare-workers-ai/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [Cohere](https://cetra.sh/docs/integrations/cohere/) | ✓ | — | `instrument=["cohere"]` | captured |
| [CometAPI](https://cetra.sh/docs/integrations/cometapi/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [Databricks](https://cetra.sh/docs/integrations/databricks/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [DeepInfra](https://cetra.sh/docs/integrations/deepinfra/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [DeepSeek](https://cetra.sh/docs/integrations/deepseek/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [Doubleword](https://cetra.sh/docs/integrations/doubleword/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [Fireworks AI](https://cetra.sh/docs/integrations/fireworks/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [Google Gen AI](https://cetra.sh/docs/integrations/google-genai/) | ✓ | — | `instrument=["google-genai"]` | captured |
| [Groq](https://cetra.sh/docs/integrations/groq/) | ✓ | — | `instrument=["groq"]` | captured |
| [Hugging Face Inference Providers](https://cetra.sh/docs/integrations/hugging-face/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [Hyperbolic](https://cetra.sh/docs/integrations/hyperbolic/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [IBM watsonx.ai](https://cetra.sh/docs/integrations/watsonx/) | ✓ | — | OpenLLMetry watsonx instrumentation | setup only |
| [LiteLLM](https://cetra.sh/docs/integrations/litellm/) | ✓ | — | `instrument=["litellm"]` | SDK test |
| [MiniMax](https://cetra.sh/docs/integrations/minimax/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [Mistral AI](https://cetra.sh/docs/integrations/mistral/) | ✓ | — | `instrument=["mistralai"]` | captured |
| [Moonshot AI (Kimi)](https://cetra.sh/docs/integrations/moonshot/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [Nebius AI Studio](https://cetra.sh/docs/integrations/nebius/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [Novita AI](https://cetra.sh/docs/integrations/novita/) | ✓ | ✓ | OpenAI client with the provider's base URL | captured |
| [NVIDIA NIM](https://cetra.sh/docs/integrations/nvidia-nim/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [Ollama](https://cetra.sh/docs/integrations/ollama/) | ✓ | — | `instrument=["ollama"]` | captured |
| [OpenAI](https://cetra.sh/docs/integrations/openai/) | ✓ | ✓ | `instrument=["openai"]` or `openai(OpenAI)` | captured |
| [OpenRouter](https://cetra.sh/docs/integrations/openrouter/) | ✓ | ✓ | OpenAI client with the provider's base URL | captured |
| [Perplexity](https://cetra.sh/docs/integrations/perplexity/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [Qwen (Alibaba Cloud Model Studio)](https://cetra.sh/docs/integrations/qwen/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [SambaNova](https://cetra.sh/docs/integrations/sambanova/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [Sarvam AI](https://cetra.sh/docs/integrations/sarvam/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [Together AI](https://cetra.sh/docs/integrations/together/) | ✓ | — | `instrument=["together"]` | captured |
| [Vertex AI](https://cetra.sh/docs/integrations/vertex-ai/) | ✓ | — | `instrument=["google-genai"]` | captured |
| [vLLM](https://cetra.sh/docs/integrations/vllm/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [Volcengine Ark](https://cetra.sh/docs/integrations/volcengine/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [xAI](https://cetra.sh/docs/integrations/xai/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |
| [Z.ai (GLM)](https://cetra.sh/docs/integrations/z-ai/) | ✓ | ✓ | OpenAI client with the provider's base URL | setup only |

- [Amazon Bedrock](https://cetra.sh/docs/integrations/bedrock/): Bedrock calls from Python.
- [Anthropic](https://cetra.sh/docs/integrations/anthropic/): Messages, streaming, thinking and cache tokens.
- [Azure OpenAI](https://cetra.sh/docs/integrations/azure-openai/): Azure OpenAI through the OpenAI SDK.
- [Cohere](https://cetra.sh/docs/integrations/cohere/): Cohere v2 chat from Python.
- [Google Gen AI](https://cetra.sh/docs/integrations/google-genai/): Gemini through the Google Gen AI SDK.
- [Groq](https://cetra.sh/docs/integrations/groq/): Groq SDK and the OpenAI client.
- [LiteLLM](https://cetra.sh/docs/integrations/litellm/): LiteLLM calls from Python.
- [Mistral AI](https://cetra.sh/docs/integrations/mistral/): Mistral AI from Python.
- [Novita AI](https://cetra.sh/docs/integrations/novita/): Novita through the OpenAI client.
- [Ollama](https://cetra.sh/docs/integrations/ollama/): Local models through the Ollama SDK.
- [OpenAI](https://cetra.sh/docs/integrations/openai/): Chat, Responses API and embeddings.
- [OpenRouter](https://cetra.sh/docs/integrations/openrouter/): Any model through OpenRouter, priced by its publisher.
- [Together AI](https://cetra.sh/docs/integrations/together/): Together SDK and the OpenAI client.
- [Vertex AI](https://cetra.sh/docs/integrations/vertex-ai/): Gemini on Vertex AI.

## Gateways

| Integration | Python | TypeScript | How | Tested against |
|---|---|---|---|---|
| [Bifrost](https://cetra.sh/docs/integrations/bifrost/) | — | — | the gateway's `otel` plugin | captured |
| [Cloudflare AI Gateway](https://cetra.sh/docs/integrations/cloudflare-ai-gateway/) | ✓ | ✓ | OpenAI client with the gateway's base URL | setup only |
| [Envoy AI Gateway](https://cetra.sh/docs/integrations/envoy-ai-gateway/) | — | — | the gateway's OpenTelemetry environment | captured |
| [Helicone](https://cetra.sh/docs/integrations/helicone/) | ✓ | ✓ | OpenAI client with the gateway's base URL | setup only |
| [Kong AI Gateway](https://cetra.sh/docs/integrations/kong/) | — | — | the `opentelemetry` plugin | setup only |
| [LiteLLM Proxy](https://cetra.sh/docs/integrations/litellm-proxy/) | ✓ | — | the proxy's `otel` callback | captured |
| [Portkey](https://cetra.sh/docs/integrations/portkey/) | ✓ | ✓ | OpenAI client with the gateway's base URL | setup only |
| [TrueFoundry](https://cetra.sh/docs/integrations/truefoundry/) | ✓ | ✓ | OpenAI client with the gateway's base URL | setup only |
| [Vercel AI Gateway](https://cetra.sh/docs/integrations/vercel-ai-gateway/) | ✓ | ✓ | OpenAI client with the gateway's base URL | setup only |

- [Bifrost](https://cetra.sh/docs/integrations/bifrost/): Chain and LLM spans per call, with the reported cost.
- [Envoy AI Gateway](https://cetra.sh/docs/integrations/envoy-ai-gateway/): One LLM span per call, priced from the table.
- [LiteLLM Proxy](https://cetra.sh/docs/integrations/litellm-proxy/): Routed calls with the cost the proxy computes.

## Frameworks

| Integration | Python | TypeScript | How | Tested against |
|---|---|---|---|---|
| [AG2](https://cetra.sh/docs/integrations/ag2/) | ✓ | — | AG2's own telemetry middleware | captured |
| [Agent Spec](https://cetra.sh/docs/integrations/agent-spec/) | ✓ | — | OpenInference's Agent Spec instrumentor | captured |
| [Agno](https://cetra.sh/docs/integrations/agno/) | ✓ | — | `instrument=["agno"]` | captured |
| [aisuite](https://cetra.sh/docs/integrations/aisuite/) | ✓ | — | the instrumentation of each provider SDK | setup only |
| [Atomic Agents](https://cetra.sh/docs/integrations/atomic-agents/) | ✓ | — | its `AtomicAgentsInstrumentor` | captured |
| [AutoGen](https://cetra.sh/docs/integrations/autogen/) | ✓ | — | `instrument=["autogen-agentchat"]` | captured |
| [Bedrock AgentCore](https://cetra.sh/docs/integrations/bedrock-agentcore/) | ✓ | — | Strands' own OpenTelemetry spans | captured |
| [BeeAI Framework](https://cetra.sh/docs/integrations/beeai/) | ✓ | — | `instrument=["beeai"]` | captured |
| [Claude Agent SDK](https://cetra.sh/docs/integrations/claude-agent-sdk/) | ✓ | ✓ | Claude Code's OpenTelemetry spans | captured |
| [Cloudflare Agents](https://cetra.sh/docs/integrations/cloudflare-agents/) | — | ✓ | Cloudflare's OTLP destination | setup only |
| [CrewAI](https://cetra.sh/docs/integrations/crewai/) | ✓ | — | `instrument=["crewai"]` | SDK test |
| [Deep Agents](https://cetra.sh/docs/integrations/deepagents/) | ✓ | — | `instrument=["langchain"]` | captured |
| [DSPy](https://cetra.sh/docs/integrations/dspy/) | ✓ | — | `instrument=["dspy"]` | SDK test |
| [eve](https://cetra.sh/docs/integrations/eve/) | — | ✓ | its `otelIntegration` | captured |
| [Flue](https://cetra.sh/docs/integrations/flue/) | — | ✓ | `@flue/opentelemetry` | captured |
| [Google ADK](https://cetra.sh/docs/integrations/google-adk/) | ✓ | — | ADK's own OpenTelemetry spans | captured |
| [Graphite](https://cetra.sh/docs/integrations/graphite/) | ✓ | — | grafi's OTLP/gRPC export | setup only |
| [Guardrails AI](https://cetra.sh/docs/integrations/guardrails/) | ✓ | — | `instrument=["openai"]` plus Guardrails' own spans | captured |
| [Haystack](https://cetra.sh/docs/integrations/haystack/) | ✓ | — | `instrument=["haystack"]` | captured |
| [Instructor](https://cetra.sh/docs/integrations/instructor/) | ✓ | — | `instrument=["instructor", "openai"]` | captured |
| [LangChain](https://cetra.sh/docs/integrations/langchain/) | ✓ | ✓ | `instrument=["langchain"]` or `langchain(…)` | captured (Python), SDK test (TypeScript) |
| [LangGraph](https://cetra.sh/docs/integrations/langgraph/) | ✓ | ✓ | `instrument=["langchain"]` | captured (Python), SDK test (TypeScript) |
| [LlamaIndex](https://cetra.sh/docs/integrations/llama-index/) | ✓ | — | `instrument=["llama-index"]` | SDK test |
| [Mastra](https://cetra.sh/docs/integrations/mastra/) | — | ✓ | Mastra's OpenTelemetry exporter | captured |
| [Microsoft Agent Framework](https://cetra.sh/docs/integrations/agent-framework/) | ✓ | — | Agent Framework's own OpenTelemetry spans | captured |
| [Mirascope](https://cetra.sh/docs/integrations/mirascope/) | ✓ | — | Mirascope's `ops` traces | captured |
| [NVIDIA NeMo Agent Toolkit](https://cetra.sh/docs/integrations/nemo-agent-toolkit/) | ✓ | — | its OTLP exporter | setup only |
| [OpenAI Agents SDK](https://cetra.sh/docs/integrations/openai-agents/) | ✓ | — | `instrument=["openai-agents"]` | captured |
| [OpenHands](https://cetra.sh/docs/integrations/openhands/) | ✓ | — | Laminar SDK's OTLP export | hand-written |
| [Pydantic AI](https://cetra.sh/docs/integrations/pydantic-ai/) | ✓ | — | Pydantic AI's own OpenTelemetry spans | captured |
| [Semantic Kernel](https://cetra.sh/docs/integrations/semantic-kernel/) | ✓ | — | Semantic Kernel's own OpenTelemetry spans | captured |
| [smolagents](https://cetra.sh/docs/integrations/smolagents/) | ✓ | — | `instrument=["smolagents"]` | captured |
| [Strands Agents](https://cetra.sh/docs/integrations/strands/) | ✓ | — | Strands' own OpenTelemetry spans | captured |
| [TanStack AI](https://cetra.sh/docs/integrations/tanstack-ai/) | — | ✓ | OpenInference's TanStack AI middleware | captured |
| [Temporal](https://cetra.sh/docs/integrations/temporal/) | ✓ | — | Temporal's OpenAI Agents plugin | captured |
| [Vercel AI SDK](https://cetra.sh/docs/integrations/vercel-ai-sdk/) | — | ✓ | `aiTelemetry()` | captured |
| [VoltAgent](https://cetra.sh/docs/integrations/voltagent/) | — | ✓ | VoltAgent's own OpenTelemetry spans | captured |
| [Spring AI](https://cetra.sh/docs/integrations/spring-ai/) | — | — | Java, Spring Boot with Micrometer or OpenInference | captured |
| [LangChain4j](https://cetra.sh/docs/integrations/langchain4j/) | — | — | Java, OpenInference's model listener | captured |
| [Quarkus LangChain4j](https://cetra.sh/docs/integrations/quarkus-langchain4j/) | — | — | Java, the `quarkus-opentelemetry` extension | captured |
| [Koog](https://cetra.sh/docs/integrations/koog/) | — | — | Kotlin, its OpenTelemetry feature | captured |
| [Microsoft.Extensions.AI](https://cetra.sh/docs/integrations/extensions-ai/) | — | — | .NET, `UseOpenTelemetry()` on the client | captured |
| [Genkit Go](https://cetra.sh/docs/integrations/genkit-go/) | — | — | Go, its `otelgenai` plugin | setup only |
| [openai-go](https://cetra.sh/docs/integrations/openai-go/) | — | — | Go, OpenInference Go's middleware | setup only |
| [ruby_llm](https://cetra.sh/docs/integrations/ruby-llm/) | — | — | Ruby, its built-in `RubyLLM::OpenTelemetry` | setup only |
| [rig](https://cetra.sh/docs/integrations/rig/) | — | — | Rust, `tracing` spans exported by `tracing-opentelemetry` | setup only |
| [Arconia](https://cetra.sh/docs/integrations/arconia/) | — | — | Java, its OpenInference starter for Spring AI | setup only |

- [AG2](https://cetra.sh/docs/integrations/ag2/): Agent runs and tool calls.
- [Agent Spec](https://cetra.sh/docs/integrations/agent-spec/): Agent runs, model calls and tool calls, without token counts.
- [Agno](https://cetra.sh/docs/integrations/agno/): Agent runs, model calls and tools.
- [Atomic Agents](https://cetra.sh/docs/integrations/atomic-agents/): Agent runs, model calls and the tools you call yourself.
- [AutoGen](https://cetra.sh/docs/integrations/autogen/): AgentChat agents, model calls and tools.
- [Bedrock AgentCore](https://cetra.sh/docs/integrations/bedrock-agentcore/): AgentCore agents, through their Strands spans.
- [BeeAI Framework](https://cetra.sh/docs/integrations/beeai/): Agents in Python.
- [Claude Agent SDK](https://cetra.sh/docs/integrations/claude-agent-sdk/): Claude Code turns inside your app.
- [CrewAI](https://cetra.sh/docs/integrations/crewai/): CrewAI from Python.
- [Deep Agents](https://cetra.sh/docs/integrations/deepagents/): LangChain Deep Agents.
- [DSPy](https://cetra.sh/docs/integrations/dspy/): DSPy from Python.
- [eve](https://cetra.sh/docs/integrations/eve/): Agent turns, steps, model calls and tools.
- [Flue](https://cetra.sh/docs/integrations/flue/): Agent runs, model calls and tools, with content on by default.
- [Google ADK](https://cetra.sh/docs/integrations/google-adk/): Agent Development Kit runs.
- [Guardrails AI](https://cetra.sh/docs/integrations/guardrails/): Guards, validators and the checked call.
- [Haystack](https://cetra.sh/docs/integrations/haystack/): Pipelines and model calls.
- [Instructor](https://cetra.sh/docs/integrations/instructor/): Structured outputs and their model calls.
- [LangChain](https://cetra.sh/docs/integrations/langchain/): Chains, agents, models and tools.
- [LangGraph](https://cetra.sh/docs/integrations/langgraph/): Graphs, nodes and tools.
- [LlamaIndex](https://cetra.sh/docs/integrations/llama-index/): LlamaIndex from Python.
- [Mastra](https://cetra.sh/docs/integrations/mastra/): Agent runs, steps and tool calls.
- [Microsoft Agent Framework](https://cetra.sh/docs/integrations/agent-framework/): Agents and workflow steps.
- [Mirascope](https://cetra.sh/docs/integrations/mirascope/): Ops traces, model calls and tool loops.
- [OpenAI Agents SDK](https://cetra.sh/docs/integrations/openai-agents/): Agent runs and function tools.
- [OpenHands](https://cetra.sh/docs/integrations/openhands/): SDK conversations, steps and tools.
- [Pydantic AI](https://cetra.sh/docs/integrations/pydantic-ai/): Agent runs, model calls and tool calls.
- [Semantic Kernel](https://cetra.sh/docs/integrations/semantic-kernel/): Agents, plugin functions and model calls.
- [smolagents](https://cetra.sh/docs/integrations/smolagents/): Agent steps, model calls and tools.
- [Strands Agents](https://cetra.sh/docs/integrations/strands/): Event loop cycles, model calls and tools.
- [TanStack AI](https://cetra.sh/docs/integrations/tanstack-ai/): Chat runs, model calls and tool calls.
- [Temporal](https://cetra.sh/docs/integrations/temporal/): Agent workflows on Temporal, with their model calls.
- [Vercel AI SDK](https://cetra.sh/docs/integrations/vercel-ai-sdk/): Versions 4 to 7.
- [VoltAgent](https://cetra.sh/docs/integrations/voltagent/): Agent runs, model calls and tools, with the session.
- [Spring AI](https://cetra.sh/docs/integrations/spring-ai/): Model calls and tools from Spring Boot, on one of two routes.
- [LangChain4j](https://cetra.sh/docs/integrations/langchain4j/): Model calls with messages, tokens and cost.
- [Quarkus LangChain4j](https://cetra.sh/docs/integrations/quarkus-langchain4j/): AI services, model calls and tools in one trace.
- [Koog](https://cetra.sh/docs/integrations/koog/): Agent runs, graph nodes, model calls and tools.
- [Microsoft.Extensions.AI](https://cetra.sh/docs/integrations/extensions-ai/): Chat calls with messages and tokens, and tool calls.

## Apps and tools

| Integration | Python | TypeScript | How | Tested against |
|---|---|---|---|---|
| [Azure AI Evaluation](https://cetra.sh/docs/integrations/azure-ai-evaluation/) | ✓ | — | `cetra.score` with the evaluators' results | setup only |
| [Browser Use](https://cetra.sh/docs/integrations/browser-use/) | ✓ | — | Pydantic AI's own OpenTelemetry spans | captured |
| [Cleanlab](https://cetra.sh/docs/integrations/cleanlab/) | ✓ | — | `cetra.score` with Cleanlab's trustworthiness score | setup only |
| [Cognee](https://cetra.sh/docs/integrations/cognee/) | ✓ | — | its built-in OTLP export | setup only |
| [EverOS](https://cetra.sh/docs/integrations/everos/) | ✓ | — | its built-in OTLP export | captured |
| [Gradio](https://cetra.sh/docs/integrations/gradio/) | ✓ | — | the instrumentation of the client library | setup only |
| [Harbor and Terminal-Bench](https://cetra.sh/docs/integrations/harbor/) | ✓ | — | Harbor's OTLP protobuf export, sent as files | setup only |
| [Langflow](https://cetra.sh/docs/integrations/langflow/) | ✓ | — | the Traceloop export that Langflow includes | captured |
| [LiveKit Agents](https://cetra.sh/docs/integrations/livekit-agents/) | ✓ | — | LiveKit's own OpenTelemetry spans | captured |
| [LobeChat](https://cetra.sh/docs/integrations/lobechat/) | — | — | LobeChat's telemetry variables | setup only |
| [MCP](https://cetra.sh/docs/integrations/mcp/) | ✓ | — | the MCP SDK's own OpenTelemetry spans | captured |
| [n8n](https://cetra.sh/docs/integrations/n8n/) | — | — | n8n's OpenTelemetry export | captured |
| [OpenLIT](https://cetra.sh/docs/integrations/openlit/) | ✓ | — | OpenLIT's own OpenTelemetry instrumentation | captured |
| [Pipecat](https://cetra.sh/docs/integrations/pipecat/) | ✓ | — | Pipecat's built-in tracing | captured |
| [Pixeltable](https://cetra.sh/docs/integrations/pixeltable/) | ✓ | — | `opentelemetry-instrumentation-pixeltable` | setup only |
| [Prompt Flow](https://cetra.sh/docs/integrations/prompt-flow/) | ✓ | — | Prompt Flow's own OpenTelemetry setup | captured |
| [Promptfoo](https://cetra.sh/docs/integrations/promptfoo/) | — | — | Promptfoo's own OpenTelemetry export | captured |
| [Ragas](https://cetra.sh/docs/integrations/ragas/) | ✓ | — | `cetra.score` with Ragas metric results | setup only |
| [Stagehand](https://cetra.sh/docs/integrations/stagehand/) | — | ✓ | the `telemetry` option of the Stagehand SDK | setup only |
| [UQLM](https://cetra.sh/docs/integrations/uqlm/) | ✓ | — | `cetra.score` with UQLM's scorer results | setup only |
| [Vector databases](https://cetra.sh/docs/integrations/vector-databases/) | ✓ | — | OpenLIT's or OpenLLMetry's vector instrumentors | captured |

- [Browser Use](https://cetra.sh/docs/integrations/browser-use/): Browser agent runs, model calls and tools.
- [EverOS](https://cetra.sh/docs/integrations/everos/): Memory add, flush and search calls, with their model and embedding calls.
- [Langflow](https://cetra.sh/docs/integrations/langflow/): Flow runs, components and model calls.
- [LiveKit Agents](https://cetra.sh/docs/integrations/livekit-agents/): Agent sessions, turns, model calls and tools.
- [MCP](https://cetra.sh/docs/integrations/mcp/): Tool calls across MCP clients and servers.
- [n8n](https://cetra.sh/docs/integrations/n8n/): Workflow runs, nodes and the tokens of AI nodes.
- [OpenLIT](https://cetra.sh/docs/integrations/openlit/): Model, embedding and vector database spans.
- [Pipecat](https://cetra.sh/docs/integrations/pipecat/): Conversations, turns and each model call.
- [Prompt Flow](https://cetra.sh/docs/integrations/prompt-flow/): Traced functions and their OpenAI calls.
- [Promptfoo](https://cetra.sh/docs/integrations/promptfoo/): Eval cases, provider calls and graders.
- [Vector databases](https://cetra.sh/docs/integrations/vector-databases/): Chroma, Qdrant, Milvus, Pinecone and Weaviate calls.

## Coding agents

| Integration | Python | TypeScript | How | Tested against |
|---|---|---|---|---|
| [Claude Code](https://cetra.sh/docs/integrations/claude-code/) | — | — | `cetra agents setup claude-code` | captured |
| [Codex CLI](https://cetra.sh/docs/integrations/codex/) | — | — | `cetra agents setup codex` | captured |
| [Gemini CLI](https://cetra.sh/docs/integrations/gemini-cli/) | — | — | `cetra agents setup gemini-cli` | captured |
| [Qwen Code](https://cetra.sh/docs/integrations/qwen-code/) | — | — | `cetra agents setup qwen-code` | captured |
| [Pi](https://cetra.sh/docs/integrations/pi/) | — | — | `cetra agents setup pi` | captured |
| [OpenClaw](https://cetra.sh/docs/integrations/openclaw/) | — | — | `cetra agents setup openclaw` | captured |
| [OpenCode](https://cetra.sh/docs/integrations/opencode/) | — | — | `cetra agents setup opencode` | captured |
| [Goose](https://cetra.sh/docs/integrations/goose/) | — | — | `cetra agents setup goose` | captured |
| [Oh My Pi](https://cetra.sh/docs/integrations/oh-my-pi/) | — | — | `cetra agents setup oh-my-pi` | captured |
| [GitHub Copilot](https://cetra.sh/docs/integrations/copilot/) | — | — | `cetra agents setup copilot` | hand-written |
| [Hermes Agent](https://cetra.sh/docs/integrations/hermes-agent/) | — | — | the `hermes-otel` community plugin | captured |
| [Augment Code](https://cetra.sh/docs/integrations/augment-code/) | — | — | its `otel` extension's environment variables | setup only |
| [Devin SDK](https://cetra.sh/docs/integrations/devin-sdk/) | — | — | the SDK's session tracing | setup only |
| [Omnigent](https://cetra.sh/docs/integrations/omnigent/) | — | — | Omnigent's telemetry environment variables | setup only |

- [Claude Code](https://cetra.sh/docs/integrations/claude-code/): Prompts, model calls and tools, with the Claude Agent SDK.
- [Codex CLI](https://cetra.sh/docs/integrations/codex/): Turns and tool calls from codex exec.
- [Gemini CLI](https://cetra.sh/docs/integrations/gemini-cli/): Model calls and tool batches.
- [Qwen Code](https://cetra.sh/docs/integrations/qwen-code/): Model calls and tool calls, with the session.
- [Pi](https://cetra.sh/docs/integrations/pi/): Prompts, model calls and tools through cetra's pi extension.
- [OpenClaw](https://cetra.sh/docs/integrations/openclaw/): Agent runs, model calls and tools through its diagnostics plugin.
- [OpenCode](https://cetra.sh/docs/integrations/opencode/): Prompts, model calls and tools through cetra's OpenCode plugin.
- [Goose](https://cetra.sh/docs/integrations/goose/): Replies, model calls and tools through its OpenTelemetry environment.
- [Oh My Pi](https://cetra.sh/docs/integrations/oh-my-pi/): Runs, model calls and tools through its .env file.
- [GitHub Copilot](https://cetra.sh/docs/integrations/copilot/): Copilot CLI and VS Code Copilot Chat.
- [Hermes Agent](https://cetra.sh/docs/integrations/hermes-agent/): Turns, model calls and tools through its telemetry plugin.

## Instrumentation libraries

| Integration | Python | TypeScript | How | Tested against |
|---|---|---|---|---|
| [OpenInference](https://cetra.sh/docs/integrations/openinference/) | ✓ | ✓ | point its exporter at cetra | captured |
| [OpenLLMetry (Traceloop)](https://cetra.sh/docs/integrations/openllmetry/) | ✓ | ✓ | point its exporter at cetra | captured |
| [OpenTelemetry GenAI](https://cetra.sh/docs/integrations/opentelemetry-genai/) | ✓ | ✓ | the official `opentelemetry-instrumentation-*` packages | captured |

- [OpenInference](https://cetra.sh/docs/integrations/openinference/): Arize's instrumentation, with any exporter.
- [OpenLLMetry](https://cetra.sh/docs/integrations/openllmetry/): Traceloop's instrumentation and SDK.
- [OpenTelemetry GenAI](https://cetra.sh/docs/integrations/opentelemetry-genai/): The official GenAI instrumentation packages.

## Migrating

| Integration | Python | TypeScript | How | Tested against |
|---|---|---|---|---|
| [From Laminar](https://cetra.sh/docs/integrations/from-laminar/) | ✓ | — | keep the Laminar SDK, export its spans to cetra | captured |
| [From Langfuse](https://cetra.sh/docs/integrations/from-langfuse/) | ✓ | — | keep the Langfuse SDK, export its spans to cetra | captured |

- [From Laminar](https://cetra.sh/docs/integrations/from-laminar/): Keep the Laminar SDK and send its spans to cetra.
- [From Langfuse](https://cetra.sh/docs/integrations/from-langfuse/): Keep the Langfuse SDK and send its spans to cetra.

"Captured" means the repository holds spans recorded from the real library, and a test checks how cetra reads them. "Hand-written" means the spans in the repository were written from the vendor's attribute reference. "SDK test" means an SDK test checks the helper, and the repository holds no recorded spans. "Setup only" means the page shows the setup; nothing in the repository records this integration's spans. A ✓ for TypeScript means a helper in `@cetra-ai/sdk`, or a TypeScript library whose spans are captured. A — means no helper or captured run exists for that language: you can still use the library's own OpenTelemetry instrumentation with [Send OpenTelemetry](https://cetra.sh/docs/get-started/opentelemetry/). A dash in both language columns means the page is for another language, named in the How column (Java, Kotlin, .NET, Go, Ruby, Rust).

Anything else that speaks OpenTelemetry works through [Send OpenTelemetry](https://cetra.sh/docs/get-started/opentelemetry/).
