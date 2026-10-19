---
component_id: api-auth-sessions
repo: api-spring
type: api-feature
description: >-
  Use when a client signs in with email and password, completes the MFA step, refreshes or revokes a bearer session through the /api/auth endpoints.
triggers:
  - "sign in"
  - "login"
  - "MFA verify"
  - "refresh token"
  - "logout"
  - "bearer token"
  - "session expiry"
  - "INVALID_CREDENTIALS"
paths:
  - "src/main/java/com/electricwealth/api/web/AuthController.java"
  - "src/main/java/com/electricwealth/api/dto/AuthDtos.java"
  - "src/main/java/com/electricwealth/api/repository/SessionRepository.java"
  - "src/main/java/com/electricwealth/api/repository/UserRepository.java"
  - "src/test/java/com/electricwealth/api/AuthApiTest.java"
depends_on:
  - "db-identity-and-auth"
  - "api-request-pipeline"
  - "api-contract-and-data-access"
consumed_by:
  - "api-registration"
  - "api-request-pipeline"
  - "web-sign-in-and-mfa"
data_entities:
  - "User"
  - "Session"
last_verified_against_commit: 3f7403e33d97b39872e89690431c7880dea98ae4
last_feature_id: ""
source_doc: docs/api-internal/12-domain-models-and-business-rules.md
verification_signal: openapi-diff
---

# Auth and Sessions

> **Repo:** `api-spring` ([repo summary](../../repos/electric-wealth-api-spring/README.md)) · **Type:** `api-feature` · **Source doc:** [`docs/api-internal/12-domain-models-and-business-rules.md`](../../../electric-wealth-api-spring/docs/api-internal/12-domain-models-and-business-rules.md)

## 1. Overview

Issues and manages opaque bearer sessions (`mock-at-…` / `mock-rt-…`) stored as rows in the shared `sessions` table, so tokens are interchangeable with the Node API. Sign-in returns an unverified token; `mfa/verify` (demo code `123456`) marks it verified and extends its 900-second life. Refresh rotates the pair and revokes the old one; logout revokes by access and/or refresh token and always returns 204.

`SessionRepository` is also used by `api-request-pipeline` (token lookup on every protected call) and by `api-registration` (signed-in session on completion).

**Operations:** `POST /api/auth/token`, `POST /api/auth/mfa/verify`, `POST /api/auth/refresh`, `POST /api/auth/logout`

**Depends on:** [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md), [`api-request-pipeline`](../../components/electric-wealth-api-spring/api-request-pipeline.md), [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md)

**Consumed by:** [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md), [`api-request-pipeline`](../../components/electric-wealth-api-spring/api-request-pipeline.md), [`web-sign-in-and-mfa`](../../components/electric-wealth-web/web-sign-in-and-mfa.md)

**Shared entities:** [`User`](../../shared/entities/user.md), [`Session`](../../shared/entities/session.md)

---

## 2. Key Responsibilities

- Validate password grant and credentials (same message for wrong email or password).
- Mark a session MFA-verified and extend its expiry.
- Rotate refresh tokens (old pair revoked; reuse → `INVALID_REFRESH`).
- Revoke sessions on logout.

**Explicitly NOT in scope:**

- Enforcing auth on protected endpoints — owned by [`api-request-pipeline`](../../components/electric-wealth-api-spring/api-request-pipeline.md)
- Creating new users — owned by [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| HTTP operations (4) in `openapi.json`, tag `Auth` | Web callers `web-sign-in-and-mfa`; web `src/api/schema.d.ts` must be regenerated; Node API parity | `openapi-diff`: `OpenApiSpecTest`, `ContractParityTest`, then `npm run verify` in `electric-wealth-web` |
| Internal refactor (no interface change) | This component's controller / repository only | `./mvnw test -Dtest=AuthApiTest` |
| Data-entity schema change | Schema owner(s) `db-identity-and-auth` and every API feature reading the same tables | `schema-diff` on `electric-wealth-db` + full `./mvnw test` |
| Dependency upgrade | Whole API (single Maven module) | `./mvnw verify` incl. `OpenApiSpecTest` |

**Upstream dependents to notify:** `api-registration`, `api-request-pipeline`, `web-sign-in-and-mfa`

**Downstream services to monitor post-change:** `db-identity-and-auth`, `api-request-pipeline`, `api-contract-and-data-access`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (2026-10-19)

**Open follow-ups from extraction:**

- Demo-grade auth: plain-text passwords and a fixed MFA code (Part A § 23).
