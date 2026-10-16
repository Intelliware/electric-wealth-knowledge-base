---
title: "Base URL and Environments"
block: reverse-eng/api/consumer/base-url-and-environments
part: B2
source_repo: electric-wealth-api-spring
source_version: 0.1.0
source_commit: 8a61cdeafb4537b16221d7aa0e9300010abd3387
extracted: 2026-10-15
---

## 3. Base URL and Environments

**Environments:**

| Environment | Base URL | Notes |
|---|---|---|
| Local development | `http://localhost:4200` | The only server listed in the spec. Port set by `PORT`. |
| Web app dev proxy | `http://localhost:5173/api/...` | The web app's Vite dev server forwards `/api` to `API_URL` (default `http://localhost:4200`). |

No staging or production environments are defined.

**Path conventions:**

- Health: `/health` (no `/api` prefix).
- Everything else under `/api/<area>/…`: `auth`, `registration`, `wealth` (dashboard, accounts, goals, documents), `profile`, `preferences`, `notifications`, `education`, `advisor`, `market`.
- No version segment in the path (see [14-versioning.md](14-versioning.md)).
- Path ids are opaque strings (e.g. `tfsa`, `d1`, `no1`, `appt-1a2b3c4d`); market symbols are case-insensitive (`xeqt.to` → `XEQT.TO`).

**TLS / network requirements:**

- Plain HTTP; no TLS is configured.
- Browser calls must come from an allowed CORS origin (default `http://localhost:5173`, set by `CORS_ORIGINS`, comma-separated). Allowed methods: GET, HEAD, PUT, PATCH, POST, DELETE; any request header; `X-Trace-Id` is exposed to scripts. Cookies/credentials are not used.

**Choosing an environment:**

Use the local API directly for scripts and tests; use the Vite proxy (same-origin `/api`) from the web app.

**Switching environments in your code:**

Read the base URL from configuration (the web app uses `API_URL`). The API's own settings are `PORT`, `DB_PATH`
and `CORS_ORIGINS` in its `.env`.
