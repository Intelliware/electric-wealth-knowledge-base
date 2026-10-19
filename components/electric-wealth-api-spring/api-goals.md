---
component_id: api-goals
repo: api-spring
type: api-feature
description: >-
  Use when a client lists or creates savings goals through /api/wealth/goals.
triggers:
  - "goals"
  - "create goal"
  - "savings goal"
  - "target amount"
  - "target date"
  - "goal progress"
paths:
  - "src/main/java/com/electricwealth/api/web/GoalController.java"
  - "src/main/java/com/electricwealth/api/dto/GoalDtos.java"
  - "src/main/java/com/electricwealth/api/repository/GoalRepository.java"
  - "src/test/java/com/electricwealth/api/WealthApiTest.java"
depends_on:
  - "db-goals-and-dashboard"
  - "api-request-pipeline"
  - "api-contract-and-data-access"
consumed_by:
  - "web-goals"
data_entities:
  - "Goal"
last_verified_against_commit: 3f7403e33d97b39872e89690431c7880dea98ae4
last_feature_id: ""
source_doc: docs/api-internal/12-domain-models-and-business-rules.md
verification_signal: openapi-diff
---

# Savings Goals

> **Repo:** `api-spring` ([repo summary](../../repos/electric-wealth-api-spring/README.md)) · **Type:** `api-feature` · **Source doc:** [`docs/api-internal/12-domain-models-and-business-rules.md`](../../../electric-wealth-api-spring/docs/api-internal/12-domain-models-and-business-rules.md)

## 1. Overview

Pages goals and creates new ones. Validation: `targetDate` after today's UTC date, `currentAmount` (default 0) ≤ `targetAmount`, maximum $100M, currency `CAD` only. `progressPct = round(current / target × 100)`. New goals are not shown on the dashboard.

**Operations:** `GET /api/wealth/goals`, `POST /api/wealth/goals`

**Depends on:** [`db-goals-and-dashboard`](../../components/electric-wealth-db/db-goals-and-dashboard.md), [`api-request-pipeline`](../../components/electric-wealth-api-spring/api-request-pipeline.md), [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md)

**Consumed by:** [`web-goals`](../../components/electric-wealth-web/web-goals.md)

**Shared entities:** [`Goal`](../../shared/entities/goal.md)

---

## 2. Key Responsibilities

- Validate and create goals.
- Compute progress percentage.
- Page the goal list.

**Explicitly NOT in scope:**

- Dashboard goal subset — owned by [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| HTTP operations (2) in `openapi.json`, tag `Goals` | Web callers `web-goals`; web `src/api/schema.d.ts` must be regenerated; Node API parity | `openapi-diff`: `OpenApiSpecTest`, `ContractParityTest`, then `npm run verify` in `electric-wealth-web` |
| Internal refactor (no interface change) | This component's controller / repository only | `./mvnw test -Dtest=WealthApiTest` |
| Data-entity schema change | Schema owner(s) `db-goals-and-dashboard` and every API feature reading the same tables | `schema-diff` on `electric-wealth-db` + full `./mvnw test` |
| Dependency upgrade | Whole API (single Maven module) | `./mvnw verify` incl. `OpenApiSpecTest` |

**Upstream dependents to notify:** `web-goals`

**Downstream services to monitor post-change:** `db-goals-and-dashboard`, `api-request-pipeline`, `api-contract-and-data-access`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
