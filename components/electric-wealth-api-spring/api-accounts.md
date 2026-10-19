---
component_id: api-accounts
repo: api-spring
type: api-feature
description: >-
  Use when a client lists, opens, renames or hides investment accounts, or reads account holdings, transactions or performance through /api/wealth/accounts.
triggers:
  - "accounts"
  - "open account"
  - "account detail"
  - "holdings"
  - "transactions"
  - "performance"
  - "nickname"
  - "hide account"
  - "TFSA"
  - "RRSP"
  - "FHSA"
paths:
  - "src/main/java/com/electricwealth/api/web/AccountController.java"
  - "src/main/java/com/electricwealth/api/dto/AccountDtos.java"
  - "src/main/java/com/electricwealth/api/repository/AccountRepository.java"
  - "src/test/java/com/electricwealth/api/WealthApiTest.java"
depends_on:
  - "db-accounts-and-investments"
  - "api-request-pipeline"
  - "api-contract-and-data-access"
consumed_by:
  - "web-accounts"
data_entities:
  - "Account"
  - "Holding"
  - "Transaction"
  - "AccountPerformance"
last_verified_against_commit: 3f7403e33d97b39872e89690431c7880dea98ae4
last_feature_id: ""
source_doc: docs/api-internal/12-domain-models-and-business-rules.md
verification_signal: openapi-diff
---

# Accounts

> **Repo:** `api-spring` ([repo summary](../../repos/electric-wealth-api-spring/README.md)) · **Type:** `api-feature` · **Source doc:** [`docs/api-internal/12-domain-models-and-business-rules.md`](../../../electric-wealth-api-spring/docs/api-internal/12-domain-models-and-business-rules.md)

## 1. Overview

Lists visible accounts (ordered by `sort_order`), returns detail with holdings, pages posted transactions (`date DESC, id`) and always returns the full list of scheduled ones, and serves a performance series against a benchmark.

Opening is limited to `TFSA`, `RRSP`, `FHSA`, `NON_REGISTERED`; a new account gets id `acct-xxxxxxxx`, zero balance, type-specific tax status and contribution room, and an empty performance row. PATCH distinguishes an absent `nickname` from `null` (clear) via the `AccountPatchRequest` class.

**Operations:** `GET /api/wealth/accounts`, `POST /api/wealth/accounts`, `GET /api/wealth/accounts/{id}`, `PATCH /api/wealth/accounts/{id}`, `GET /api/wealth/accounts/{id}/transactions`, `GET /api/wealth/accounts/{id}/performance`

**Depends on:** [`db-accounts-and-investments`](../../components/electric-wealth-db/db-accounts-and-investments.md), [`api-request-pipeline`](../../components/electric-wealth-api-spring/api-request-pipeline.md), [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md)

**Consumed by:** [`web-accounts`](../../components/electric-wealth-web/web-accounts.md)

**Shared entities:** [`Account`](../../shared/entities/account.md), [`Holding`](../../shared/entities/holding.md), [`Transaction`](../../shared/entities/transaction.md), [`AccountPerformance`](../../shared/entities/account-performance.md)

---

## 2. Key Responsibilities

- List and page accounts and transactions.
- Open new accounts with type-specific defaults.
- Update nickname and visibility.
- Serve performance vs benchmark.

**Explicitly NOT in scope:**

- Portfolio total on the dashboard — owned by [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md)
- Security prices — owned by [`api-market`](../../components/electric-wealth-api-spring/api-market.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| HTTP operations (6) in `openapi.json`, tag `Accounts` | Web callers `web-accounts`; web `src/api/schema.d.ts` must be regenerated; Node API parity | `openapi-diff`: `OpenApiSpecTest`, `ContractParityTest`, then `npm run verify` in `electric-wealth-web` |
| Internal refactor (no interface change) | This component's controller / repository only | `./mvnw test -Dtest=WealthApiTest` |
| Data-entity schema change | Schema owner(s) `db-accounts-and-investments` and every API feature reading the same tables | `schema-diff` on `electric-wealth-db` + full `./mvnw test` |
| Dependency upgrade | Whole API (single Maven module) | `./mvnw verify` incl. `OpenApiSpecTest` |

**Upstream dependents to notify:** `web-accounts`

**Downstream services to monitor post-change:** `db-accounts-and-investments`, `api-request-pipeline`, `api-contract-and-data-access`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (2026-10-19)

**Open follow-ups from extraction:**

- `range` on the performance endpoint is echoed but does not filter the series (Part A § 12.4).
