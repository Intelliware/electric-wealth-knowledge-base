---
component_id: api-request-pipeline
repo: api-spring
type: framework
description: >-
  Use when a change touches how every API request is authenticated, traced, rate-limited or turned into the standard error envelope — the bearer-token filter, Spring Security config, trace and rate-limit filters, or GlobalExceptionHandler.
triggers:
  - "bearer token filter"
  - "authentication"
  - "401 UNAUTHORIZED"
  - "rate limit"
  - "RATE_LIMITED"
  - "trace id"
  - "error envelope"
  - "exception handler"
  - "CORS"
  - "security config"
paths:
  - "src/main/java/com/electricwealth/api/security/**"
  - "src/main/java/com/electricwealth/api/error/**"
  - "src/main/java/com/electricwealth/api/web/TraceFilter.java"
  - "src/main/java/com/electricwealth/api/web/RateLimitFilter.java"
  - "src/main/java/com/electricwealth/api/web/TraceIds.java"
  - "src/main/java/com/electricwealth/api/config/WebConfig.java"
depends_on:
  - "api-auth-sessions"
  - "api-contract-and-data-access"
consumed_by:
  - "api-accounts"
  - "api-advisor"
  - "api-auth-sessions"
  - "api-dashboard"
  - "api-documents"
  - "api-education"
  - "api-goals"
  - "api-health"
  - "api-market"
  - "api-notifications"
  - "api-profile-and-preferences"
  - "api-registration"
data_entities:
  - "Session"
last_verified_against_commit: 3f7403e33d97b39872e89690431c7880dea98ae4
last_feature_id: ""
source_doc: docs/api-internal/07-middleware-and-request-pipeline.md
verification_signal: exported-symbols-diff
---

# Request Pipeline, Security and Errors

> **Repo:** `api-spring` ([repo summary](../../repos/electric-wealth-api-spring/README.md)) · **Type:** `framework` · **Source doc:** [`docs/api-internal/07-middleware-and-request-pipeline.md`](../../../electric-wealth-api-spring/docs/api-internal/07-middleware-and-request-pipeline.md)

## 1. Overview

Every request passes `TraceFilter` (assigns `traceId`), `RateLimitFilter` (300 requests per minute per IP, fixed window, no rate-limit headers) and, under protected prefixes, `BearerTokenFilter`, which looks the token up in `sessions` and requires it to exist, be unrevoked, unexpired and MFA-verified. There are no roles or scopes and the API never returns 403.

`GlobalExceptionHandler` maps every failure to `{ "error": { code, message, traceId } }` with the same codes and statuses as the Node API (`VALIDATION_ERROR`, `UNAUTHORIZED`, `NOT_FOUND`, `RATE_LIMITED`, `INTERNAL_ERROR`, plus feature codes).

**Depends on:** [`api-auth-sessions`](../../components/electric-wealth-api-spring/api-auth-sessions.md), [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md)

**Consumed by:** [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md), [`api-advisor`](../../components/electric-wealth-api-spring/api-advisor.md), [`api-auth-sessions`](../../components/electric-wealth-api-spring/api-auth-sessions.md), [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md), [`api-documents`](../../components/electric-wealth-api-spring/api-documents.md), [`api-education`](../../components/electric-wealth-api-spring/api-education.md), [`api-goals`](../../components/electric-wealth-api-spring/api-goals.md), [`api-health`](../../components/electric-wealth-api-spring/api-health.md), [`api-market`](../../components/electric-wealth-api-spring/api-market.md), [`api-notifications`](../../components/electric-wealth-api-spring/api-notifications.md), [`api-profile-and-preferences`](../../components/electric-wealth-api-spring/api-profile-and-preferences.md), [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md)

**Shared entities:** [`Session`](../../shared/entities/session.md)

---

## 2. Key Responsibilities

- Authenticate bearer tokens on protected prefixes.
- Assign trace ids.
- Apply the per-IP rate limit.
- Render the canonical error envelope.
- Configure CORS.

**Explicitly NOT in scope:**

- Issuing and rotating tokens — owned by [`api-auth-sessions`](../../components/electric-wealth-api-spring/api-auth-sessions.md)
- Request DTO validation annotations — owned by [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Exported symbols / shared behaviour | Every dependant: `api-accounts`, `api-advisor`, `api-auth-sessions`, `api-dashboard`, `api-documents`, `api-education`, `api-goals`, `api-health`, `api-market`, `api-notifications`, `api-profile-and-preferences`, `api-registration` | `exported-symbols-diff`, then the repo's full gate |
| Internal refactor (no interface change) | This component only | Repo gate (`./mvnw test` or `npm run verify`) |
| Data-entity schema change | Indirect — via the features that use this foundation | Repo gate |
| Dependency upgrade | Whole repo | `./mvnw verify` or `npm run build` |

**Upstream dependents to notify:** `api-accounts`, `api-advisor`, `api-auth-sessions`, `api-dashboard`, `api-documents`, `api-education`, `api-goals`, `api-health`, `api-market`, `api-notifications`, `api-profile-and-preferences`, `api-registration`

**Downstream services to monitor post-change:** `api-auth-sessions`, `api-contract-and-data-access`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (2026-10-19)

**Open follow-ups from extraction:**

- Rate limiting sends no `X-RateLimit-*` / `Retry-After` headers (Part B § 10).
