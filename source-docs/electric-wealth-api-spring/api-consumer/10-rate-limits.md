---
title: "Rate Limits"
block: reverse-eng/api/consumer/rate-limits
part: B2
source_repo: electric-wealth-api-spring
source_version: 0.1.0
source_commit: 8a61cdeafb4537b16221d7aa0e9300010abd3387
extracted: 2026-10-15
---

## 10. Rate Limits

**Rate-limit model:** Fixed one-minute window per client IP address, counted across all endpoints.

**Limits by tier:**

| Tier | Limit |
|---|---|
| Everyone (no tiers) | 300 requests per minute per IP (server setting `ew.rate-limit-per-minute`) |

**Per-endpoint cost (token cost):**

Every request costs 1, including `/health`, `/openapi.json` and `/docs`, and including requests that later fail
authentication.

**Returned headers (on EVERY response):**

None. No `X-RateLimit-*` or `Retry-After` headers are sent. A blocked request gets:

```json
HTTP 429
{ "error": { "code": "RATE_LIMITED", "message": "Too many requests. Please try again later.", "traceId": "trace-…" } }
```

**Recommended client strategy:**

- On 429, wait until the current minute window has passed (up to 60 s) before retrying; use exponential backoff with jitter if repeated.
- Avoid polling loops; cache reference data (news, baskets, glossary) on the client.

**Distinct limit dimensions:**

Client IP only. Not per user or per token. All clients behind the same proxy/NAT share one budget.

**Long-running / bulk endpoints:**

None.

**Operational guidance:**

The window starts with the first request from an IP and resets 60 s later. Limits are kept in the memory of each
API instance and reset on restart.
