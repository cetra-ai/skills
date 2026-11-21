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
| `gen_ai.operation.name` | Span kind and the operation |
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
| `gen_ai.usage.cost`, `gen_ai.usage.total_cost`, `gen_ai.usage.input_cost`, `gen_ai.usage.output_cost` | Reported cost |
| `gen_ai.response.time_to_first_chunk` | Time to first token |
| `gen_ai.conversation.id` | Session |

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

### Resource attributes

| Attribute | Feeds |
|---|---|
| `service.name` | Service name |
| `service.version` | Release |
| `deployment.environment.name`, `deployment.environment` | Environment |
