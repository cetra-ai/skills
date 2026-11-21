<!-- Generated from website/src/content/docs/docs/help/troubleshooting.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# Troubleshooting

Find the cause of the common problems, from missing traces to rejected requests, by symptom, with a check and a fix for each.

This page lists problems by what you see. Each one has a check and a fix.

## No traces at all

**Check:** Look at the dot in the top bar of the UI. Its tooltip says "Receiving spans" with the time of the last batch, "No spans in the last 5 minutes", or "No spans received since the server started". If spans arrive but the list is empty, open the **Include** menu in the list toolbar: the list hides evaluation traces and experiment traces until you tick them, and a time range or filter can hide the rest. If no spans arrive: is the API key right, and is it for the project you are looking at? Does the SDK reach the server? Look at the cetra log (`cetra serve` output or `docker logs cetra`): it names the reason when it refuses a request.

**Fix:** Use a key that starts with `sk-cetra-`, set `CETRA_BASE_URL` to the server's address (the SDKs default to `http://localhost:8080`), and make sure `CETRA_ENABLED` is not `false`. See the [quick start](https://cetra.sh/docs/get-started/quickstart/#nothing-showing-up).

## The SDK warns "no API key"

**Check:** The warning reads `cetra: no API key; set CETRA_API_KEY, or CETRA_ENABLED=false to silence this; tracing is disabled`.

**Fix:** Set `CETRA_API_KEY` or pass `api_key` to `init()`. To run without tracing on purpose, set `CETRA_ENABLED=false`. See [Python SDK](https://cetra.sh/docs/reference/python/#initialize).

## A short script loses its last spans

**Check:** Does the process exit right after the last call? Spans are exported in the background, and a process that ends first never sends them.

**Fix:** Call `flush()` (Python) or `await flush()` (TypeScript) before the process ends. See [Serverless](https://cetra.sh/docs/reference/python/opentelemetry/#serverless).

## Spans are lost on serverless or an edge runtime

**Check:** Does the platform freeze or stop the process when the handler returns?

**Fix:** Call `await flush()` or `shutdown()` at the end of every invocation. The TypeScript SDK installs no signal handlers. See [Runtimes](https://cetra.sh/docs/reference/typescript/runtimes/).

## Traces appear without input or output

**Check:** Is `capture_content` turned off, or is a mask removing the values? Does your instrumentation send prompts and completions as log events? If it does, the UI shows a banner once per project: "GenAI content was sent as log events." It gives the number of dropped records, and you can dismiss it.

**Fix:** Turn `capture_content` back on, or review your `mask`. cetra stores spans only, and drops logs and metrics, so set your instrumentation to put content on spans. See [Masking, sampling and truncation](https://cetra.sh/docs/tracing/privacy-controls/) and [OTLP endpoint](https://cetra.sh/docs/reference/otlp/#logs-and-metrics).

## The tree is flat or spans are in the wrong place

**Check:** Does each span carry the right parent span ID? A span whose parent never arrived shows as a root of its own.

**Fix:** Make sure the work runs inside the parent's context, and that you flush so every span arrives. See [Traces](https://cetra.sh/docs/tracing/traces/) and [Context](https://cetra.sh/docs/reference/python/context/).

## A span has no cost

**Check:** Does the span have token counts and a model name? Cost is reported by your code, or computed from a price that matches the model and provider.

**Fix:** Send token counts, or set a price for your model in the project. See [Cost and tokens](https://cetra.sh/docs/tracing/cost-and-tokens/).

## Spans are rejected, or the time looks wrong

**Check:** cetra rejects a span that starts before 2000-01-01, before the project's retention, or more than one day in the future. A host with a wrong clock sends such spans. The response is `200` with a `partial_success` message.

**Fix:** Correct the clock of the machine that runs your application. See [Partial success](https://cetra.sh/docs/reference/otlp/#partial-success).

## Ingest answers 401, 402, 403, 413, 429 or 503

| Status | Check | Fix |
|---|---|---|
| `401` | The key is missing or invalid. | Send `Authorization: Bearer sk-cetra-…`. |
| `402` | The organization reached its monthly units cap. | Do not retry. Wait for the reset time in `Retry-After`, or raise the cap. See [Plans and licence](https://cetra.sh/docs/platform/plans-and-licence/). |
| `403` | The credential is not a key with the `ingest` scope. | Create an API key with that scope. See [API keys and tokens](https://cetra.sh/docs/platform/api-keys/). |
| `413` | The request is over a limit: its size or its span count. | Send smaller batches, or raise the limit in [`ingest`](https://cetra.sh/docs/self-hosting/configuration-reference/#ingest). |
| `429` | The organization's ingest rate limit. | Wait for `Retry-After`. |
| `503` | The server is overloaded, out of disk space or shutting down. | Retry after `Retry-After`. Check free disk and the [resources](https://cetra.sh/docs/self-hosting/resources/). |

See the full list in [OTLP endpoint](https://cetra.sh/docs/reference/otlp/#answers).

## OTLP/gRPC does not work through a proxy

**Check:** Does the proxy speak HTTP/2 to cetra? Is port 4317 published?

**Fix:** Configure the proxy to use HTTP/2 to port 4317, or send OTLP/HTTP to port 8080 or 4318 instead. See [TLS](https://cetra.sh/docs/self-hosting/configuration/#tls).

## The setup link is lost

**Check:** cetra logs `setup required: open http://localhost:8080/setup?token=…` on its first start with an empty database.

**Fix:** Find it in the log: `docker logs cetra 2>&1 | grep 'setup required'`. To skip the page, set the `CETRA_INIT_*` variables. See [Install](https://cetra.sh/docs/self-hosting/install/#binary).

## The UI is missing

**Check:** Run `cetra version`. A build without the web UI prints `ui: not built`.

**Fix:** Use a release binary or the Docker image, which include the UI. See [Install](https://cetra.sh/docs/self-hosting/install/).

## Still stuck

See [Support and security](https://cetra.sh/docs/help/support/).
