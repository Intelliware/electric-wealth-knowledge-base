# Cross-cutting: Error handling

> **Owning repo:** `api-spring` · **Primary pointer:** [`source-docs/electric-wealth-api-spring/api-consumer/11-error-handling.md`](../../source-docs/electric-wealth-api-spring/api-consumer/11-error-handling.md)

One envelope `{ "error": { code, message, traceId } }` with codes identical to the Node API; feature codes such as `SLOT_UNAVAILABLE`, `STEP_OUT_OF_ORDER`, `EMAIL_TAKEN`. The web client unwraps it into `ApiError`, retries once on network/5xx only, and maps known codes to messages. SQLite busy/constraint errors are covered by the database consumer guide.

**Further reading:**

- [`05-error-handling-and-recovery.md`](../../source-docs/electric-wealth-db/database-consumer/05-error-handling-and-recovery.md) (Part B)

> Pointer page only — the standard itself lives in the linked source docs. Do not copy it here.
