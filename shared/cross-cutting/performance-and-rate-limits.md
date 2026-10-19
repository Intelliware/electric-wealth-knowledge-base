# Cross-cutting: Performance & rate limits

> **Owning repo:** `api-spring` · **Primary pointer:** [`source-docs/electric-wealth-api-spring/api-consumer/10-rate-limits.md`](../../source-docs/electric-wealth-api-spring/api-consumer/10-rate-limits.md)

300 requests per minute per client IP, fixed window, across all endpoints, with no rate-limit headers (blocked requests get 429 `RATE_LIMITED`). The database is a single SQLite file with a 4-connection pool and a 3 s busy timeout; the web app polls notifications every 60 s.

**Further reading:**

- [`06-performance-guidelines-for-developers.md`](../../source-docs/electric-wealth-db/database-consumer/06-performance-guidelines-for-developers.md) (Part B)
- [`09-performance-considerations.md`](../../source-docs/electric-wealth-web/web-app-consumer/09-performance-considerations.md) (Part B)

> Pointer page only — the standard itself lives in the linked source docs. Do not copy it here.
