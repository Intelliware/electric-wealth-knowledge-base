---
title: "Quickstart"
block: reverse-eng/api/consumer/quickstart
part: B2
source_repo: electric-wealth-api-spring
source_version: 0.1.0
source_commit: 8a61cdeafb4537b16221d7aa0e9300010abd3387
extracted: 2026-10-15
---

## 1. Quickstart

**API at a glance:**

| | |
|---|---|
| What | REST/JSON API behind the Electric Wealth web app: sign-in with MFA, enrolment, dashboard, accounts, goals, documents, profile, notifications, advisor booking/messaging, education, market data |
| Base URL (local) | `http://localhost:4200` |
| Contract | [openapi.yaml](../openapi.yaml) (OpenAPI 3.1.0, 49 operations); live at `GET /openapi.json`, Swagger UI at `/docs` |
| Auth | Bearer access token obtained by password sign-in **plus** MFA |
| Money | Integers in minor units (cents); currency `CAD` |
| Errors | Always `{ "error": { "code", "message", "traceId" } }` |

**Prerequisites:**

- The API running locally (`./mvnw spring-boot:run` in `electric-wealth-api-spring`, JDK 21) with the database built by `electric-wealth-db` (`npm run db:reset`).
- A demo persona. All use password `Demo123!` and MFA code `123456`:
  `sarah.chen@example.com`, `michael.thompson@example.com`, `linda.rodriguez@example.com`, `david.kim@example.com`, `emma.wilson@example.com`, `robert.foster@example.com`.

**Your first call:**

```bash
# 1. Password sign-in → token that still needs MFA
curl -sS -X POST http://localhost:4200/api/auth/token \
  -H "Content-Type: application/json" \
  -d '{"grantType":"password","username":"sarah.chen@example.com","password":"Demo123!"}'

# 2. Complete MFA with the access token from step 1
curl -sS -X POST http://localhost:4200/api/auth/mfa/verify \
  -H "Authorization: Bearer <accessToken>" -H "Content-Type: application/json" \
  -d '{"challengeId":"mfa-1","code":"123456","method":"sms"}'

# 3. Call a protected endpoint
curl -sS http://localhost:4200/api/wealth/dashboard -H "Authorization: Bearer <accessToken>"
```

**Expected response (HTTP 200):**

Step 1 returns `{ "accessToken": "mock-at-…", "refreshToken": "mock-rt-…", "tokenType": "Bearer", "expiresIn": 900, "mfaRequired": true }`.
Step 3 returns the dashboard: `{ "portfolio": {…}, "allocation": [...], "goals": [...], "alerts": [...], "quickLinks": [...] }`.

Unauthenticated health check: `curl -sS http://localhost:4200/health` → `{"status":"ok"}`.

**If you got a non-200, jump to:** [11-error-handling.md](11-error-handling.md)

**Next steps:**

1. [02-authentication.md](02-authentication.md) — token lifetime, refresh, logout.
2. [04-endpoint-catalog.md](04-endpoint-catalog.md) — every endpoint.
3. [05-endpoint-detail-template.md](05-endpoint-detail-template.md) — request/response detail per endpoint.
