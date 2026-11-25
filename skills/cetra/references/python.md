<!-- Generated from website/src/content/docs/docs/reference/python/index.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# Python SDK

Install the cetra Python SDK, configure it and find your way around its reference pages.

Use the Python SDK to send traces and scores from your Python code to cetra. `cetra` is a thin layer over OpenTelemetry: spans go out as OTLP/HTTP protobuf to `<base_url>/v1/traces`, and scores go to `<base_url>/api/v1/scores`. It needs Python 3.11 or later and is MIT licensed.

The server stores traces only. If another OpenTelemetry GenAI instrumentation sends prompts and completions as log events, they reach `/v1/logs` and are dropped; the project shows a hint in the UI. Set that instrumentation to capture message content on spans.

The pages of this reference:

- [Tracing](https://cetra.sh/docs/reference/python/tracing/): `observe`, manual spans and `llm_span`.
- [Context](https://cetra.sh/docs/reference/python/context/): sessions, users, tags and metadata, baggage and threads.
- [Scores, datasets and experiments](https://cetra.sh/docs/reference/python/scores-datasets-experiments/): `score`, the `Client` and `evaluate`.
- [OpenTelemetry](https://cetra.sh/docs/reference/python/opentelemetry/): instrumentors, existing OpenTelemetry setups, masking, serverless and known limits.
- [API](https://cetra.sh/docs/reference/python/api/): every public name.

## Install

```bash
pip install cetra-sdk
```

Auto-instrumentation is optional, one extra per library:

| `instrument` name | Extra |
|---|---|
| `openai` | `cetra-sdk[openai]` |
| `anthropic` | `cetra-sdk[anthropic]` |
| `langchain` | `cetra-sdk[langchain]` |
| `llama-index` | `cetra-sdk[llama-index]` |
| `litellm` | `cetra-sdk[litellm]` |
| `google-genai` | `cetra-sdk[google-genai]` |
| `bedrock` | `cetra-sdk[bedrock]` |
| `openai-agents` | `cetra-sdk[openai-agents]` |
| `crewai` | `cetra-sdk[crewai]` |
| `dspy` | `cetra-sdk[dspy]` |
| `groq` | `cetra-sdk[groq]` |
| `mistralai` | `cetra-sdk[mistralai]` |
| `cohere` | `cetra-sdk[cohere]` |
| `ollama` | `cetra-sdk[ollama]` |
| `together` | `cetra-sdk[together]` |
| `instructor` | `cetra-sdk[instructor]` |
| `agno` | `cetra-sdk[agno]` |
| `smolagents` | `cetra-sdk[smolagents]` |
| `haystack` | `cetra-sdk[haystack]` |
| `beeai` | `cetra-sdk[beeai]` |
| `mcp` | `cetra-sdk[mcp]` |
| `autogen-agentchat` | `pip install openinference-instrumentation-autogen-agentchat` |

The `instructor` and `beeai` distributions do not support every Python version: the `instructor` extra installs on Python 3.13 or earlier, and `beeai` on Python 3.14 or earlier.

`cetra-sdk[all]` installs every instrumentor that has an extra. The core package pulls in only `opentelemetry-api`, `opentelemetry-sdk`, `opentelemetry-exporter-otlp-proto-http` and `requests`.

## Initialize

For a first trace, follow the [quick start](https://cetra.sh/docs/get-started/quickstart/).

`init()` is called once per process; it also registers an `atexit` hook that calls `shutdown()` (flush, then stop). `flush()` is the manual lever when you need the data out now: see [Serverless](https://cetra.sh/docs/reference/python/opentelemetry/#serverless).

Without an API key (argument and `CETRA_API_KEY` both unset), `init()` logs one warning (`cetra: no API key; set CETRA_API_KEY, or CETRA_ENABLED=false to silence this; tracing is disabled`) and the SDK runs as a no-op: nothing raises, nothing is sent. Set `CETRA_ENABLED=false` to silence the warning.

## Configuration

An explicit argument wins over the environment variable, which wins over the default. An argument set to `None` counts as not given; env values are trimmed and an empty value counts as unset.

| Option | Env | Default |
|---|---|---|
| `api_key` | `CETRA_API_KEY` | unset: one warning, SDK disabled |
| `base_url` | `CETRA_BASE_URL` | `http://localhost:8080` |
| `environment` | `CETRA_ENVIRONMENT` | unset |
| `release` | `CETRA_RELEASE` | unset (falls back to `GIT_COMMIT`, then `SOURCE_VERSION`) |
| `capture_content` | `CETRA_CAPTURE_CONTENT` | `true` |
| `sample_rate` | `CETRA_SAMPLE_RATE` | `1.0` |
| `instrument` | — | `[]` |
| `mask` | — | `None` |
| `export_filter` | — | `None` |
| `use_global_provider` | — | `False` |
| `enabled` | `CETRA_ENABLED` | `true` (no-op mode when false) |

- `enabled` is resolved first; when it is false, no other option is read or validated.
- Booleans from the environment accept `true`/`false`, `1`/`0`, `yes`/`no`, `on`/`off`, case-insensitive.
- `api_key` must start with `sk-cetra-`. A key with the wrong prefix raises; a missing key does not (see above).
- `base_url` must be an absolute `http`/`https` URL without query or fragment. A path is allowed (reverse-proxy prefix); trailing slashes are removed.
- `environment` must not be a reserved environment: `cetra-eval` (evaluation) or `cetra-playground` (playground runs).
- `sample_rate` must be a finite number in `[0, 1]` (a `ParentBased(TraceIdRatioBased)` sampler on our own provider). From the environment it must be a plain decimal (`1`, `0.25`, `.5`).
- Any other invalid value raises `ConfigError` (a `ValueError` subclass) naming the option or env var, never its value. Resolution stops at the first problem.
- `init()` is once per process: any later call, whatever the first one did, logs `cetra: init() was already called; this call is ignored` and changes nothing.

## What is never logged

The SDK's own logs and errors never contain payload content (prompts, completions, attribute values), secrets, API keys or email addresses. Log lines carry ids, sizes, counts and exception type names only; configuration errors name the option, never its value; the API key is excluded from `repr` and serialization of the config. Messages go to the `cetra` logger, which has no handlers by default: configure the standard `logging` module to see them. The missing-key and second-`init()` warnings appear once per process; a full score buffer warns at most once per minute, and rejected or failed score batches are logged as they happen.

## User agent

Every request the SDK sends carries `User-Agent: cetra-sdk-python/<version>`: score writes, prompt fetches and `Client` calls. The trace exporter keeps the OpenTelemetry exporter's own value and adds the SDK's after it (`OTel-OTLP-Exporter-Python/<otel version> cetra-sdk-python/<version>`). Nothing else goes into it. A firewall or proxy in front of cetra can allow these values.
