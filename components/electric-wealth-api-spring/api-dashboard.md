---
component_id: api-dashboard
repo: api-spring
type: api-feature
description: >-
  Use when the signed-in user's portfolio summary, asset allocation, dashboard goals, action-required alerts or quick links are served by GET /api/wealth/dashboard.
triggers:
  - "dashboard"
  - "portfolio summary"
  - "total value"
  - "asset allocation"
  - "alerts"
  - "quick links"
  - "day change"
paths:
  - "src/main/java/com/electricwealth/api/web/DashboardController.java"
  - "src/main/java/com/electricwealth/api/dto/DashboardDtos.java"
  - "src/main/java/com/electricwealth/api/repository/DashboardRepository.java"
  - "src/main/java/com/electricwealth/api/web/Labels.java"
  - "src/test/java/com/electricwealth/api/WealthApiTest.java"
depends_on:
  - "db-goals-and-dashboard"
  - "db-accounts-and-investments"
  - "api-request-pipeline"
  - "api-contract-and-data-access"
consumed_by:
  - "web-dashboard"
data_entities:
  - "PortfolioSnapshot"
  - "AssetAllocation"
  - "Alert"
  - "QuickLink"
  - "Goal"
  - "Account"
last_verified_against_commit: 3f7403e33d97b39872e89690431c7880dea98ae4
last_feature_id: ""
source_doc: docs/api-internal/12-domain-models-and-business-rules.md
verification_signal: openapi-diff
---

# Dashboard

> **Repo:** `api-spring` ([repo summary](../../repos/electric-wealth-api-spring/README.md)) · **Type:** `api-feature` · **Source doc:** [`docs/api-internal/12-domain-models-and-business-rules.md`](../../../electric-wealth-api-spring/docs/api-internal/12-domain-models-and-business-rules.md)

## 1. Overview

Assembles one response from the snapshot, allocation, alert, quick-link and goal tables. `totalValue` is computed live from visible account balances; the rest comes from `portfolio_snapshots` (404 if the user has none). Only goals with `show_on_dashboard = 1` appear. Quick-link labels are translated to French by `Labels` when `Accept-Language` starts with `fr`.

**Operations:** `GET /api/wealth/dashboard`

**Depends on:** [`db-goals-and-dashboard`](../../components/electric-wealth-db/db-goals-and-dashboard.md), [`db-accounts-and-investments`](../../components/electric-wealth-db/db-accounts-and-investments.md), [`api-request-pipeline`](../../components/electric-wealth-api-spring/api-request-pipeline.md), [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md)

**Consumed by:** [`web-dashboard`](../../components/electric-wealth-web/web-dashboard.md)

**Shared entities:** [`PortfolioSnapshot`](../../shared/entities/portfolio-snapshot.md), [`AssetAllocation`](../../shared/entities/asset-allocation.md), [`Alert`](../../shared/entities/alert.md), [`QuickLink`](../../shared/entities/quick-link.md), [`Goal`](../../shared/entities/goal.md), [`Account`](../../shared/entities/account.md)

---

## 2. Key Responsibilities

- Compute the live portfolio total.
- Return allocation, alerts and quick links with mobile deep links.
- Localise quick-link labels.

**Explicitly NOT in scope:**

- Goal creation — owned by [`api-goals`](../../components/electric-wealth-api-spring/api-goals.md)
- Account detail — owned by [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| HTTP operations (1) in `openapi.json`, tag `Dashboard` | Web callers `web-dashboard`; web `src/api/schema.d.ts` must be regenerated; Node API parity | `openapi-diff`: `OpenApiSpecTest`, `ContractParityTest`, then `npm run verify` in `electric-wealth-web` |
| Internal refactor (no interface change) | This component's controller / repository only | `./mvnw test -Dtest=WealthApiTest` |
| Data-entity schema change | Schema owner(s) `db-goals-and-dashboard`, `db-accounts-and-investments` and every API feature reading the same tables | `schema-diff` on `electric-wealth-db` + full `./mvnw test` |
| Dependency upgrade | Whole API (single Maven module) | `./mvnw verify` incl. `OpenApiSpecTest` |

**Upstream dependents to notify:** `web-dashboard`

**Downstream services to monitor post-change:** `db-goals-and-dashboard`, `db-accounts-and-investments`, `api-request-pipeline`, `api-contract-and-data-access`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
