# Cross-cutting: Logging & Observability

> **Owning repo:** `api-spring` · **Primary pointer:** [`source-docs/electric-wealth-api-spring/api-consumer/11-error-handling.md`](../../source-docs/electric-wealth-api-spring/api-consumer/11-error-handling.md)

Every API response carries a `trace-xxxxxxxx` id (header `X-Trace-Id` and `error.traceId`); the web app shows it on `ErrorState`. No metrics, tracing backend or log aggregation exists. Part B covers the client-visible trace id; the internal logging setup is in the API's Part A (source repo).

**Further reading:**

- [`21-observability-logging-metrics-tracing.md`](../../../electric-wealth-api-spring/docs/api-internal/21-observability-logging-metrics-tracing.md) (Part A, source repo)

> Pointer page only — the standard itself lives in the linked source docs. Do not copy it here.
