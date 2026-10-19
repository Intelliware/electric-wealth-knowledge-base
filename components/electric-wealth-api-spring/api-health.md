---
component_id: api-health
repo: api-spring
type: api-feature
description: >-
  Use when a client, load balancer or developer needs the API liveness check at GET /health.
triggers:
  - "health check"
  - "liveness"
  - "GET /health"
  - "is the API up"
  - "health endpoint"
paths:
  - "src/main/java/com/electricwealth/api/web/HealthController.java"
  - "src/main/java/com/electricwealth/api/dto/HealthResponse.java"
  - "src/test/java/com/electricwealth/api/AuthApiTest.java"
depends_on:
  - "api-request-pipeline"
  - "api-contract-and-data-access"
consumed_by: []
data_entities: []
last_verified_against_commit: 3f7403e33d97b39872e89690431c7880dea98ae4
last_feature_id: ""
source_doc: docs/api-internal/12-domain-models-and-business-rules.md
verification_signal: openapi-diff
---

# Health Endpoint

> **Repo:** `api-spring` ([repo summary](../../repos/electric-wealth-api-spring/README.md)) · **Type:** `api-feature` · **Source doc:** [`docs/api-internal/12-domain-models-and-business-rules.md`](../../../electric-wealth-api-spring/docs/api-internal/12-domain-models-and-business-rules.md)

## 1. Overview

Public liveness probe returning `HealthResponse`. It goes through the trace and rate-limit filters like every request but needs no token. The web app does not call it.

**Operations:** `GET /health`

**Depends on:** [`api-request-pipeline`](../../components/electric-wealth-api-spring/api-request-pipeline.md), [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md)

---

## 2. Key Responsibilities

- Report process liveness without touching user data.

**Explicitly NOT in scope:**

- Database connectivity diagnostics — owned by [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| HTTP operations (1) in `openapi.json`, tag `Health` | Web callers (none); web `src/api/schema.d.ts` must be regenerated; Node API parity | `openapi-diff`: `OpenApiSpecTest`, `ContractParityTest`, then `npm run verify` in `electric-wealth-web` |
| Internal refactor (no interface change) | This component's controller / repository only | `./mvnw test -Dtest=AuthApiTest` |
| Data-entity schema change | Schema owner(s) (none) and every API feature reading the same tables | `schema-diff` on `electric-wealth-db` + full `./mvnw test` |
| Dependency upgrade | Whole API (single Maven module) | `./mvnw verify` incl. `OpenApiSpecTest` |

**Upstream dependents to notify:** None modelled.

**Downstream services to monitor post-change:** `api-request-pipeline`, `api-contract-and-data-access`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
