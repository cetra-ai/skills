<!-- Generated from website/src/content/docs/docs/reference/otlp.mdx by website/scripts/skill-references.mjs. Do not edit: run `just docs-gen`. -->

# OTLP endpoint

Where to send OpenTelemetry spans to cetra, the encodings it accepts, its limits and what each answer means.

cetra ingests spans over OTLP, the OpenTelemetry protocol, from the cetra SDKs or from any OpenTelemetry exporter.

## Endpoints

| Transport | Endpoint | Encodings |
|---|---|---|
| OTLP/HTTP | `POST /v1/traces` on the main port (default 8080) and on port 4318 | `application/x-protobuf`, `application/json`; `Content-Encoding` of `gzip`, `zstd`, `deflate` or none |
| OTLP/gRPC | `opentelemetry.proto.collector.trace.v1.TraceService/Export` on port 4317 | protobuf; gzip |

Port 4318 serves only the OTLP routes and `/v1/agent-context`, for exporters that use the standard OpenTelemetry port.

OTLP/JSON follows the OpenTelemetry specification strictly: lowerCamelCase field names, hex trace and span IDs, enums as integers and 64-bit integers as decimal strings or numbers. A body with enum names or base64 IDs gets `400`.

## Authentication

Send an API key that has the `ingest` scope as a bearer token. The key chooses the project; there is no project header.

```bash
export OTEL_EXPORTER_OTLP_ENDPOINT="http://localhost:8080"
export OTEL_EXPORTER_OTLP_HEADERS="Authorization=Bearer <API_KEY>"
```

## Limits

| Limit | Default | When exceeded |
|---|---|---|
| HTTP request body, before and after decompression (`ingest.max_request_bytes`) | 10 MiB | `413` |
| gRPC message (`ingest.max_grpc_message_bytes`) | 32 MiB | gRPC `OUT_OF_RANGE` |
| Spans in one request (`ingest.max_spans_per_request`) | 50,000 | `413`, gRPC `INVALID_ARGUMENT` |
| One attribute value (`ingest.max_attribute_bytes`) | 8 MiB | The value is cut and marked as truncated |
| Request bytes held in memory at once (`ingest.max_inflight_bytes`) | 256 MiB | `503` with `Retry-After: 1` |

The limits are set in the server configuration.

## Answers

| Status | Meaning |
|---|---|
| `200` | Every accepted span is on disk. The body is an OTLP `ExportTraceServiceResponse` in the request's encoding. |
| `400` | The body is malformed. |
| `401` | No key, or an invalid one. |
| `402` | The org's monthly units cap is reached. Do not retry; `Retry-After` is the seconds until the cap resets. |
| `403` | The credential is not a key with the `ingest` scope. |
| `413` | The request is over a limit above. |
| `415` | Unknown `Content-Type` or `Content-Encoding`. |
| `429` | The org's ingest rate limit; wait for `Retry-After`. |
| `503` | The server is overloaded, out of disk space or shutting down; retry after `Retry-After`. |

Error bodies are `application/problem+json` as described in the [REST API](https://cetra.sh/docs/reference/api/overview/#errors), not an OTLP status message. gRPC uses the matching status codes: `UNAUTHENTICATED`, `PERMISSION_DENIED`, `INVALID_ARGUMENT`, `RESOURCE_EXHAUSTED` for the units cap and `UNAVAILABLE` for overload and the rate limit.

When the units cap is reached, the cetra SDKs stop sending until the reset time instead of retrying.

## Partial success

cetra rejects a span, and accepts the others in the request, when:

- its trace ID or span ID is missing, has the wrong length or is all zeros;
- a start or end time does not fit in a signed 64-bit integer of nanoseconds;
- its start time is before 2000-01-01, older than the project's retention, or more than one day in the future.

The response is `200` with `partial_success.rejected_spans` set and an `error_message` that lists the reasons and counts, for example `rejected 3 spans: invalid_span_id ×2, start_time_out_of_range ×1`. The message never contains a value from your request.

Some problems are fixed instead of rejected. A parent span ID of all zeros or of the wrong length counts as no parent. An end time before the start time is set equal to the start time, and a warning is recorded on the span.

## Logs and metrics

`POST /v1/logs` and `POST /v1/metrics` (and the gRPC equivalents) are accepted on the same ports, with the same authentication and limits, and then dropped. cetra stores spans only. If your instrumentation sends prompts and completions as log events, set it to capture them on spans.
