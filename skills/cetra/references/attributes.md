<!-- Generated from website/src/content/docs/docs/reference/attributes.md by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# Span attributes

Every cetra attribute and every convention attribute cetra reads from a span, with its type and meaning.

Use this page to look up which span attribute sets which field in cetra. For how the conventions fit together, see [Semantic conventions](https://cetra.sh/docs/concepts/conventions/).

## cetra attributes

The cetra SDKs set these attributes. You can also set them yourself on any OpenTelemetry span. cetra reads them before any other convention.

| Attribute | Type | Set by | Meaning |
|---|---|---|---|
| `cetra.span.kind` | string | `observe`, `span`, `llm_span` | The span kind: `llm`, `embedding`, `tool`, `agent`, `chain`, `retriever`, `reranker`, `guardrail`, `evaluator` or `span`. |
| `cetra.input` | string | `observe`, `span` | The span's input: text, JSON or a list of chat messages. |
| `cetra.input.format` | string | `observe`, `span` | How to read `cetra.input`: `messages`, `json` or `text`. |
| `cetra.output` | string | `observe`, `span` | The span's output, in the same formats as the input. |
| `cetra.output.format` | string | `observe`, `span` | How to read `cetra.output`: `messages`, `json` or `text`. |
| `cetra.session.id` | string | `context` and `withContext` | The session the span's trace belongs to. |
| `cetra.user.id` | string | `context` and `withContext` | The user the span's trace belongs to. |
| `cetra.tags` | string list | `context` and `withContext` | Tags on the trace. A list of strings, or a string holding a JSON list of strings. |
| `cetra.metadata.<key>` | string | `context` and `withContext`, `set_metadata` | One metadata entry. The key after the prefix is the metadata key. Keys use letters, digits, `_`, `.` and `-`. |
| `cetra.environment` | string | `init` | The environment, such as `production`. |
| `cetra.release` | string | `init` | The release: a version or a Git SHA. |
| `cetra.ttft_ns` | integer | `record_first_token` | Time to first token, in nanoseconds. |
| `cetra.cost.total` | float | `set_cost` | The total cost in USD, reported by your code. cetra does not compute a cost when it is present. |
| `cetra.experiment.id` | string | `evaluate` | The experiment a span belongs to. |
| `cetra.dataset_item.id` | string | `evaluate` | The dataset item an experiment run used. |
| `cetra.prompt.name` | string | `context` and `compile` | The registry prompt the span used. |
| `cetra.prompt.version` | integer | `context` and `compile` | The version of that prompt, 1 or higher. |

## Set by cetra

cetra writes these attributes on its own spans, for example the traces of evaluators and the warnings of normalization. You never set them. A value you send under one of these names is replaced.

| Attribute | Type | Meaning |
|---|---|---|
| `cetra.experiment_id` | string | The experiment of a span, stored in its metadata. Copied from `cetra.experiment.id`. |
| `cetra.dataset_item_id` | string | The dataset item of a span, stored in its metadata. Copied from `cetra.dataset_item.id`. |
| `cetra.eval.target_trace_id` | string | On an evaluator's trace: the trace being evaluated. |
| `cetra.eval.target_span_id` | string | On an evaluator's trace: the span being evaluated, when the target is a span. |
| `cetra.eval.rule_id` | string | On an evaluator's trace: the online rule that ran it. |
| `cetra.eval.experiment_id` | string | On an evaluator's trace: the experiment it scored. |
| `cetra.eval.evaluator_id` | string | On an evaluator's trace: the evaluator. |
| `cetra.assistant.user` | string | The user whose assistant turn the trace records. Set by the server on `cetra-assistant` traces. |
| `cetra.assistant.conversation_id` | string | The assistant conversation of the turn. |
| `cetra.assistant.prompt_version` | string | The version of the assistant's built-in system prompt. |
| `cetra.playground.run_by` | string | On a playground run's trace: the user who ran it. |
| `cetra.playground.source` | string | On a playground run's trace: the prompt it used, `prompt:<name>@<version>`, or the span it came from, `span:<trace>:<span>`. |
| `cetra.normalize_warning` | string list | Warning codes for recoverable problems found while reading the span, such as `end_before_start`. |
| `cetra.normalize_error` | string | Codes for a payload that cetra recognized but could not convert. The raw value is kept. |

## Conventions cetra reads

When a span carries no cetra attribute for a field, cetra reads the field from the first of these conventions that has a value. [Semantic conventions](https://cetra.sh/docs/concepts/conventions/) lists the order.

### OpenTelemetry GenAI

| Attribute | Feeds |
|---|---|
| `gen_ai.operation.name` | Span kind and the operation; `embeddings`, `embedding` and `aembedding` are embedding operations; `tts` and `stt` (Pipecat's speech operations) are plain `span`s |
| `gen_ai.provider.name`, `gen_ai.system` | Provider |
| `gen_ai.request.model` | Request model |
| `gen_ai.response.model` | Response model |
| `gen_ai.request.*` | Request parameters |
| `gen_ai.input.messages` | Input messages |
| `gen_ai.output.messages` | Output messages |
| `gen_ai.system_instructions` | System instructions |
| `gen_ai.tool.definitions` | Tool definitions |
| `gen_ai.tool.call.arguments`, `gen_ai.tool.call.result` | Input and output of a tool span |
| `gen_ai.response.finish_reasons` | Finish reason of an output message |
| `gen_ai.usage.input_tokens`, `gen_ai.usage.output_tokens`, `gen_ai.usage.total_tokens` | Token counts |
| `gen_ai.usage.cache_read.input_tokens`, `gen_ai.usage.cache_creation.input_tokens` | Cache token counts |
| `gen_ai.usage.reasoning.output_tokens` | Reasoning token count |
| `gen_ai.usage.cost`, `gen_ai.usage.total_cost`, `gen_ai.usage.input_cost`, `gen_ai.usage.output_cost`, `gen_ai.cost.total_cost`, `gen_ai.cost.input_cost`, `gen_ai.cost.output_cost` | Reported cost |
| `bifrost.cost.input`, `bifrost.cost.output` | Reported cost parts, on Bifrost gateway spans |
| `gen_ai.response.time_to_first_chunk` | Time to first token |
| `gen_ai.conversation.id` | Session |

### Endpoint

| Attribute | Feeds |
|---|---|
| `server.address`, `url.full`, `gen_ai.openai.api_base` | Provider, when the provider is missing or `openai` on an `llm` or `embedding` span. The host names the provider, for example `openrouter.ai` gives `openrouter`. |

### VoltAgent

Read on spans that VoltAgent writes: spans with a `voltagent.*` attribute, or whose instrumentation scope starts with `@voltagent/`.

| Attribute | Feeds |
|---|---|
| `span.type` | Span kind: `llm` and `tool` |
| `entity.type` | Span kind `agent`, when `span.type` is missing |
| `llm.model`, `ai.model.name` | Request model, after `gen_ai.request.model` |
| `llm.usage.prompt_tokens`, `llm.usage.completion_tokens` | Input and output token counts, on `llm` spans |
| `llm.usage.cached_tokens` | Cache read token count, on `llm` spans |
| `llm.usage.reasoning_tokens` | Reasoning token count, on `llm` spans |
| `llm.messages` | Input messages, on `llm` spans (AI SDK messages) |
| `input`, `output` | Input, when `llm.messages` does not supply it, and output |
| `conversation.id`, `voltagent.conversation_id` | Session |

### OpenInference

| Attribute | Feeds |
|---|---|
| `openinference.span.kind` | Span kind |
| `llm.model_name`, `embedding.model_name` | Request model |
| `llm.response.model_name` | Response model |
| `llm.provider`, `llm.system` | Provider |
| `llm.input_messages.N.message.*`, `input.value` | Input |
| `llm.output_messages.N.message.*`, `output.value` | Output |
| `input.mime_type`, `output.mime_type` | Format of `input.value` and `output.value` |
| `llm.tools.N.tool.json_schema` | Tool definitions |
| `llm.invocation_parameters` | Request parameters |
| `llm.token_count.prompt`, `llm.token_count.completion`, `llm.token_count.total` | Token counts |
| `llm.token_count.prompt_details.cache_read`, `llm.token_count.prompt_details.cache_write` | Cache token counts |
| `llm.token_count.completion_details.reasoning` | Reasoning token count |
| `llm.cost.total`, `llm.cost.prompt`, `llm.cost.completion` | Reported cost |
| `session.id`, `user.id` | Session and user |
| `metadata`, `tag.tags` | Metadata and tags |

### OpenLLMetry

| Attribute | Feeds |
|---|---|
| `traceloop.span.kind` | Span kind |
| `llm.request.type` | Span kind |
| span name, on the vector scopes | Span kind `retriever` for the read names, on the scopes `opentelemetry.instrumentation.chromadb`, `.qdrant`, `.milvus`, `.pinecone` and `.weaviate`: `chroma.query`, `chroma.get`, `qdrant.query_points`, `qdrant.search`, `qdrant.scroll`, `milvus.search`, `milvus.query`, `milvus.get`, `pinecone.query`, `pinecone.fetch`, and Weaviate names starting `db.weaviate.collections.query.` or `db.weaviate.gql.query.`. Every other name on them gives `span` |
| `gen_ai.prompt.N.*` | Input messages |
| `gen_ai.completion.N.*` | Output messages |
| `traceloop.entity.input`, `traceloop.entity.output` | Input and output when there are no messages |
| `llm.request.functions.N.*` | Tool definitions |
| `traceloop.association.properties.session_id`, `traceloop.association.properties.user_id` | Session and user |
| `traceloop.association.properties.*` | Metadata |
| `traceloop.workflow.name` | Metadata key `workflow` |

### Vercel AI SDK

| Attribute | Feeds |
|---|---|
| `ai.operationId` | Span kind |
| `ai.model.id`, `ai.response.model` | Request and response model |
| `ai.model.provider` | Provider |
| `ai.prompt.messages`, `ai.prompt` | Input |
| `ai.response.text`, `ai.response.toolCalls`, `ai.response.object` | Output |
| `ai.toolCall.args`, `ai.toolCall.result` | Input and output of a tool span |
| `ai.prompt.tools` | Tool definitions |
| `ai.usage.inputTokens`, `ai.usage.outputTokens`, `ai.usage.totalTokens` | Token counts |
| `ai.usage.inputTokenDetails.cacheReadTokens`, `ai.usage.inputTokenDetails.cacheWriteTokens` | Cache token counts |
| `ai.usage.outputTokenDetails.reasoningTokens` | Reasoning token count |
| `ai.response.msToFirstChunk` | Time to first token |
| `ai.settings.*` | Request parameters |
| `ai.telemetry.metadata.sessionId`, `ai.telemetry.metadata.userId` | Session and user |
| `ai.telemetry.metadata.tags` | Tags |
| `ai.telemetry.metadata.*`, `ai.telemetry.functionId` | Metadata |

### Langfuse

| Attribute | Feeds |
|---|---|
| `langfuse.observation.type` | Span kind |
| `langfuse.observation.input`, `langfuse.trace.input` | Input |
| `langfuse.observation.output`, `langfuse.trace.output` | Output |
| `langfuse.observation.model.name` | Request model |
| `langfuse.observation.model.parameters` | Request parameters |
| `langfuse.observation.usage_details` | Token counts |
| `langfuse.observation.cost_details` | Reported cost |
| `langfuse.observation.completion_start_time` | Time to first token |
| `langfuse.observation.level`, `langfuse.observation.status_message` | Status and status message |
| `langfuse.session.id`, `langfuse.user.id` | Session and user |
| `langfuse.trace.tags` | Tags |
| `langfuse.trace.metadata.*` | Metadata |
| `langfuse.environment`, `langfuse.release` | Environment and release |

### Laminar

| Attribute | Feeds |
|---|---|
| `lmnr.span.type` | Span kind |
| `lmnr.span.input`, `lmnr.span.output` | Input and output |
| `lmnr.association.properties.session_id`, `lmnr.association.properties.user_id` | Session and user |
| `lmnr.association.properties.tags` | Tags |
| `lmnr.association.properties.metadata.*` | Metadata |
| `lmnr.span.release` | Release |

### Google ADK

| Attribute | Feeds |
|---|---|
| `gcp.vertex.agent.llm_request` | Input messages, system instructions and tool definitions |
| `gcp.vertex.agent.llm_response` | Output message, finish reason and token counts |
| `gcp.vertex.agent.tool_call_args`, `gcp.vertex.agent.tool_response` | Input and output of a tool span |
| `gcp.vertex.agent.session_id` | Session |

### Prompt Flow

Read on spans of scope `promptflow`, which Prompt Flow writes.

| Attribute | Feeds |
|---|---|
| `span_type` | Span kind: `LLM` gives `llm`, `Embedding` gives `embedding`, `Retrieval` gives `retriever`, `Flow` and `LangChain` give `chain`, and `Function` gives `span` |
| `inputs` | Input. On an `llm` span, the messages of the OpenAI request when it has them. The `model` field of the JSON gives the request model on an `llm` or `embedding` span |
| `output` | Output. On an `llm` span, the message of the OpenAI response when it has one |
| `llm.response.model` | Response model, on an `llm` or `embedding` span |
| `llm.usage.prompt_tokens`, `llm.usage.completion_tokens`, `llm.usage.total_tokens` | Token counts, on an `llm` span |

### Langflow

Read on spans of scope `langflow`, which Langflow writes through Traceloop.

| Attribute | Feeds |
|---|---|
| `trace_type` | Span kind: `agent`, `tool`, `retriever` and `embedding` keep their kind, and any other value gives `chain` |
| `flow_id` | Span kind `chain`, when `trace_type` is missing (the flow's root span) |
| `inputs` | Input, as JSON text |
| `outputs` | Output, as JSON text |

### n8n

Read on spans of scope `n8n-workflow`. n8n keeps every node span's name as `node.execute`.

| Attribute | Feeds |
|---|---|
| span name `workflow.execute` | Span kind `chain` |
| span name `node.execute` | Span kind `span`, unless `gen_ai.operation.name` is set. Then the GenAI operation decides the kind |

### LiveKit Agents

Read on spans of scope `livekit-agents`. The `lk.pii.*` keys are present only when LiveKit keeps the content, which is the default.

| Attribute | Feeds |
|---|---|
| `lk.pii.user_input`, `lk.pii.chat_ctx`, `lk.pii.function_tool.arguments` | Input, in that order, when no GenAI key gives one |
| `lk.pii.response.text`, `lk.pii.function_tool.output` | Output, in that order, when no GenAI key gives one |
| `gen_ai.provider.name` | Provider, on any span. A host in this key is an endpoint: `api.openai.com` gives `openai`, another host that cetra maps gives its provider, and an unmapped host gives no provider |

### Pipecat

Read on spans of scopes starting with `pipecat`.

| Attribute | Feeds |
|---|---|
| `input` | Input. An OpenAI-shaped message list is read as messages. Any other value is kept as text |
| `output` | Output, as text |
| `conversation.id` | Session, on spans of scope `pipecat.turn` (the `conversation` and `turn` spans) |

### OpenLIT

Read on spans of scopes starting with `openlit.instrumentation.`, which OpenLIT writes.

| Attribute | Feeds |
|---|---|
| `gen_ai.operation.type` | Span kind, when `gen_ai.operation.name` is not a standard operation: `invoke_agent` gives `agent`, and `execute_task` gives `chain` |
| `db.operation.name` | Span kind on a vector span (`gen_ai.operation.name` `vectordb`): `get`, `query` or `search`, lowercased, gives `retriever`; any other value, and a missing key, give `span`. The key is peeked, not consumed |

The provider `browser_use`, which OpenLIT writes for Browser Use, is a framework name. cetra never reads it as a provider.

### Promptfoo

Read on spans of scope `promptfoo.providers`. Promptfoo's request and response bodies are not read.

| Attribute | Feeds |
|---|---|
| `gen_ai.evaluation.name` | Span kind `evaluator`, on a grader span |

### Resource attributes

| Attribute | Feeds |
|---|---|
| `service.name` | Service name |
| `service.version` | Release |
| `deployment.environment.name`, `deployment.environment` | Environment |
