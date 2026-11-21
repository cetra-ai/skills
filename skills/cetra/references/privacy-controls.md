<!-- Generated from website/src/content/docs/docs/tracing/privacy-controls.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# Masking, sampling and truncation

Control what your SDK sends, with content capture, masking, sampling and export filters, and what cetra cuts when a value is too large.

Use these controls to keep sensitive content out of cetra, to send fewer traces, and to understand what cetra cuts.

There are two places to act. The SDK controls what leaves your process. The server cuts values that are too large when it receives them.

## Turn off content capture

With content capture off, cetra records that a call happened, with its model, parameters, token counts, cost and timing, but not its input or output. The SDK drops the payloads of your spans, and the payload attributes of spans from other instrumentation libraries.

**Python**

```python
import cetra

cetra.init(capture_content=False)

@cetra.observe(name="answer")
def answer(question: str) -> str:
    return "42"

answer("why?")

cetra.flush()
```

**TypeScript**

```ts
import { init } from "@cetra-ai/sdk";

init({ apiKey: process.env.CETRA_API_KEY, captureContent: false });
```

You can also set `CETRA_CAPTURE_CONTENT=false` in the environment.

## Mask values

A mask is a function that receives each value the SDK records, before it is sent, and returns the value to send instead. It runs on inputs, outputs, metadata values and messages. For spans from other instrumentation libraries, it runs on the payload attributes when the span is exported. The mask gets the decoded value: text, or a parsed JSON structure.

**Python**

```python
import re

import cetra

SECRET = re.compile(r"sk-live-\w+")

def redact(value):
    if isinstance(value, str):
        return SECRET.sub("[key]", value)
    return value

cetra.init(mask=redact)

@cetra.observe(name="answer")
def answer(question: str) -> str:
    return "done"

answer("my key is sk-live-abc123")

cetra.flush()
```

**TypeScript**

```ts
import { init } from "@cetra-ai/sdk";

init({
  apiKey: process.env.CETRA_API_KEY,
  mask: (value) => (typeof value === "string" ? value.replaceAll("sk-live-", "[key]") : value),
});
```

The mask fails closed. If it raises an error, the value becomes `[cetra: mask failed]`, and a batch that cannot be masked is dropped, not sent in clear.

## Sample traces

Sampling keeps a share of your traces. The decision is made per trace, from the trace ID, so a trace is kept whole or dropped whole.

```bash
export CETRA_SAMPLE_RATE=0.25
```

`CETRA_SAMPLE_RATE` is a number from 0 to 1, and the default is 1. You can also pass `sample_rate` (Python) or `sampleRate` (TypeScript) to `init()`. Scores need their trace, so a run with a low sample rate can lose the scores of dropped traces.

## Export filters

When the SDK shares the global OpenTelemetry provider (`use_global_provider` in Python, `useGlobalProvider` in TypeScript), it sends only LLM-related spans by default. Spans from your web framework or database are left out. Pass `export_filter` (Python) or `exportFilter` (TypeScript) to choose your own rule, in either mode. It receives each finished span and returns whether to send it.

## Truncation on the server

cetra cuts values that are too large, so one huge attribute cannot fill the storage. It never rejects a span for the size of a value.

The limit for one value is 8 MiB by default. It applies to inputs, outputs, system instructions, tool definitions, attributes, metadata values, tags and names. The server's `ingest.max_attribute_bytes` setting changes it. See the [configuration reference](https://cetra.sh/docs/self-hosting/configuration-reference/).

What a cut value looks like:

- A text value is cut and ends with `…[cetra: truncated, original N bytes]`, where N is the original size.
- For a list of messages, cetra keeps the system messages, the first user message and the most recent messages that fit, and replaces each removed run with the message `[cetra: K messages truncated]`.
- A JSON input or output that is cut becomes text, because the cut value is no longer valid JSON.
- A value nested more than 32 levels deep is replaced by `[cetra: max depth]`.

The UI shows a **truncated at ingest** badge on a cut input or output. The `truncated` column of `spans` lists the names of the cut values. The Python SDK also cuts a payload larger than 1 MiB before it sends it, and marks it the same way.

A request is limited separately by the OTLP endpoint. See [OTLP endpoint](https://cetra.sh/docs/reference/otlp/).

## The assistant

The in-app assistant sends your questions, the page context, the chat history and the results of the tools it calls to the provider of the connection it uses. Its chats are private to you and are deleted 30 days after their last activity. Its turns are traced in the `cetra-assistant` environment with no message text. See [Assistant](https://cetra.sh/docs/platform/assistant/) for what is stored and for how long.

## Deleting data

To remove data cetra already stored, by user, trace, filter, project or organization, see [Deletion and retention](https://cetra.sh/docs/platform/deletion-and-retention/).
