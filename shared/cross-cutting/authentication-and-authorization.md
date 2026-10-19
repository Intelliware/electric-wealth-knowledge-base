# Cross-cutting: Authentication & Authorization

> **Owning repo:** `api-spring` · **Primary pointer:** [`source-docs/electric-wealth-api-spring/api-consumer/02-authentication.md`](../../source-docs/electric-wealth-api-spring/api-consumer/02-authentication.md)

Demo-grade custom bearer auth: password sign-in, then MFA (fixed code `123456`) before a token is accepted; 900-second access tokens with refresh rotation; opaque tokens stored in the shared `sessions` table. No roles or scopes — every MFA-verified token sees only its own user's data. The web app keeps tokens in memory, refreshes every 10 minutes and signs out after 15 idle minutes or on any 401. Components: `api-auth-sessions`, `api-request-pipeline`, `web-auth-session`, `db-identity-and-auth`.

**Further reading:**

- [`05-authentication-integration.md`](../../source-docs/electric-wealth-web/web-app-consumer/05-authentication-integration.md) (Part B)

> Pointer page only — the standard itself lives in the linked source docs. Do not copy it here.
