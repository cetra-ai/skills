<!-- Generated from website/src/content/docs/docs/integrations/openai-agents.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# OpenAI Agents SDK

Trace the OpenAI Agents SDK with cetra from Python: agent runs, turns, model calls and function tools.

cetra traces OpenAI Agents SDK runs as a tree of agent, turn, model call and tool spans.

## Install

```bash
pip install "cetra-sdk[openai-agents]" openai-agents
```

## Set up

```python
import cetra
from agents import Agent, Runner, function_tool

cetra.init(instrument=["openai-agents"])

@function_tool
def get_weather(city: str) -> str:
    return f"It is 18 degrees and sunny in {city}."

agent = Agent(
    name="weather",
    instructions="You are a weather assistant.",
    tools=[get_weather],
)
Runner.run_sync(agent, "What is the weather in Paris?")

cetra.flush()
```

## What you get

With the OpenInference instrumentation, one run with a function tool produces:

- an `agent` span for the workflow and one for the agent;
- one `chain` span per turn;
- an `llm` span for each model call, with the provider, token counts and cost;
- a `tool` span for the function tool.

## Limits

- The fixtures were captured with `openai-agents` 0.24.0, OpenInference 2.7.0 and OpenLLMetry 0.62.4.
- The SDK's own tracing to OpenAI is a separate feature. This page covers the OpenTelemetry instrumentation.

## Next

- [Traces](https://cetra.sh/docs/tracing/traces/): read the trace as a tree and a timeline.
- [Cost and tokens](https://cetra.sh/docs/tracing/cost-and-tokens/): how cost is computed from the token counts.
- [Online rules](https://cetra.sh/docs/evaluation/online-rules/): score these traces automatically.
