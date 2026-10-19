# Cross-cutting: Security standards

> **Owning repo:** `api-spring` · **Primary pointer:** [`source-docs/electric-wealth-api-spring/api-consumer/02-authentication.md`](../../source-docs/electric-wealth-api-spring/api-consumer/02-authentication.md)

Workshop/demo system: plain-text passwords and MFA codes in the database, fixed MFA code, CORS restricted to configured origins, user scoping by `user_id` in every query. Part B carries the secrets-handling rules for clients; each repo's Part A has its security-implementation chapter. Do not reuse this scheme in production.

**Further reading:**

- [`23-security-implementation.md`](../../../electric-wealth-api-spring/docs/api-internal/23-security-implementation.md) (Part A, source repo)
- [`17-security-implementation.md`](../../../electric-wealth-web/docs/web-app-internal/17-security-implementation.md) (Part A, source repo)
- [`12-security-and-access-control.md`](../../../electric-wealth-db/docs/database-internal/12-security-and-access-control.md) (Part A, source repo)

> Pointer page only — the standard itself lives in the linked source docs. Do not copy it here.
