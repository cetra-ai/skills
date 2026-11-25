<!-- Generated from website/src/content/docs/docs/integrations/langchain.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# LangChain

Trace LangChain chains, agents, models and tools with cetra from Python and TypeScript, through the OpenInference LangChain instrumentation.

cetra traces LangChain and LangGraph runs as a tree: the graph, its nodes, each model call and each tool call. For LangGraph graphs and Deep Agents, see [LangGraph](https://cetra.sh/docs/integrations/langgraph/) and [Deep Agents](https://cetra.sh/docs/integrations/deepagents/).

## Install

**Python**

```bash
pip install "cetra-sdk[langchain]" langchain langchain-openai
```

**TypeScript**

```bash
npm install @cetra-ai/sdk @arizeai/openinference-instrumentation-langchain @langchain/core @langchain/openai
```

## Set up

**Python**

```python
import cetra
from langchain_openai import ChatOpenAI

cetra.init(instrument=["langchain"])

model = ChatOpenAI(model="gpt-4o-mini")
model.invoke("What is the weather in Paris?")

cetra.flush()
```

**TypeScript**

```ts
import { init } from "@cetra-ai/sdk";
import { langchain } from "@cetra-ai/sdk/langchain";
import * as callbacksManager from "@langchain/core/callbacks/manager";
```

```ts
init({ apiKey: process.env.CETRA_API_KEY, instrument: [langchain(callbacksManager)] });
```

```ts
import { ChatOpenAI } from "@langchain/openai";

const model = new ChatOpenAI({ model: "gpt-4o-mini" });
await model.invoke("What is the weather in Paris?");
```

## What you get

- A chat model call is an `llm` span with the provider, the messages, the tool definitions and the token counts.
- A tool run is a `tool` span.
- A LangGraph agent is a tree: the graph, each node as a `chain` span, the model calls and the tool calls under their nodes.
- LangChain's run metadata, such as `langgraph_node` and `langgraph_step`, is kept as span metadata.
- Cost is computed from the token counts of each model call.

## LangServe

LangServe serves LangChain runnables over HTTP and has no spans of its own. Instrument the app's LangChain code as this page shows, and add an OpenTelemetry FastAPI instrumentor for the HTTP layer if you want HTTP spans.

## Limits

- The fixtures were captured in Python with `langchain` 1.4.8, `langchain-openai` 1.7.3 and `langgraph` 1.2.19, with OpenInference 0.1.81 and OpenLLMetry 0.62.4.
- With OpenLLMetry, the graph and its nodes come out as `agent` and `chain` spans with the provider `langgraph`.
- The TypeScript helper is covered by an SDK test that builds the instrumentation from LangChain's callback module. No spans of a LangChain.js run are captured.

## Next

- [Traces](https://cetra.sh/docs/tracing/traces/): read the trace as a tree and a timeline.
- [Cost and tokens](https://cetra.sh/docs/tracing/cost-and-tokens/): how cost is computed from the token counts.
- [Online rules](https://cetra.sh/docs/evaluation/online-rules/): score these traces automatically.
