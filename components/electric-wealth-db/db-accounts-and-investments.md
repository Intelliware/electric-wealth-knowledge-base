---
component_id: db-accounts-and-investments
repo: db
type: database-schema
description: >-
  Use when a change touches investment accounts, holdings, transactions or account performance data — columns, composite (user_id, account_id) keys, money-in-cents conventions or seeded portfolio data.
triggers:
  - "accounts table"
  - "holdings"
  - "transactions"
  - "account performance"
  - "performance points"
  - "account balance"
  - "contribution room"
  - "composite account key"
paths:
  - "migrations/001_initial_schema.sql"
  - "seed/00[1-6]_*.sql"
depends_on:
  - "db-identity-and-auth"
consumed_by:
  - "api-accounts"
  - "api-dashboard"
  - "api-market"
data_entities:
  - "Account"
  - "Holding"
  - "Transaction"
  - "AccountPerformance"
last_verified_against_commit: e6262fcddca7f4c0488248ca8881483d9b920f0a
last_feature_id: ""
source_doc: docs/database-internal/05-tables-detailed-documentation.md
verification_signal: schema-diff
---

# Accounts and Investments Schema

> **Repo:** `db` ([repo summary](../../repos/electric-wealth-db/README.md)) · **Type:** `database-schema` · **Source doc:** [`docs/database-internal/05-tables-detailed-documentation.md`](../../../electric-wealth-db/docs/database-internal/05-tables-detailed-documentation.md)

## 1. Overview

Holds each user's investment accounts and their children. `accounts` uses the composite key `(user_id, id)` because business ids such as `tfsa` are unique per user only; `holdings`, `transactions`, `account_performance` and `performance_points` hang off it through composite FKs with `ON DELETE CASCADE`.

All money is INTEGER minor units next to a `currency` column. Holdings reference securities by free-text `symbol` (no FK to `securities`). Only `accounts` and `account_performance` are written by the API (account opening, nickname/visibility); holdings, transactions and performance points come from seed data.

Read by `api-accounts`, `api-dashboard` (live total of visible balances) and `api-market` (the `held` flag).

**Tables:** `accounts`, `holdings`, `transactions`, `account_performance`, `performance_points`

**Depends on:** [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md)

**Consumed by:** [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md), [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md), [`api-market`](../../components/electric-wealth-api-spring/api-market.md)

**Shared entities:** [`Account`](../../shared/entities/account.md), [`Holding`](../../shared/entities/holding.md), [`Transaction`](../../shared/entities/transaction.md), [`AccountPerformance`](../../shared/entities/account-performance.md)

---

## 2. Key Responsibilities

- Define account, holding, transaction and performance tables with composite user-scoped keys.
- Enforce enumerations and 0/1 booleans with CHECK constraints (account type, hidden flag, transaction type/status).
- Carry the seed portfolios for the six demo personas.

**Explicitly NOT in scope:**

- Account-opening rules (types, contribution room) — owned by [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md)
- Security master data and prices — owned by [`db-market-data`](../../components/electric-wealth-db/db-market-data.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Tables `accounts`, `holdings`, `transactions`, `account_performance`, `performance_points` | Direct SQL readers `api-accounts`, `api-dashboard`, `api-market`; Node API (`electric-wealth-api`) shares the same file | `schema-diff` (data dictionary vs `pragma_table_info`), then `./mvnw test` in `electric-wealth-api-spring` |
| Internal refactor (no interface change) | Seed data / indexes only | `npm run db:reset` then `npm run db:status` |
| Data-entity schema change | New migration required; every API repository touching these tables | Part A § 20 new-table checklist + API test suite |
| Dependency upgrade | Node `node:sqlite` / SQLite version affects all schema components | `npm run db:reset` + API test suite |

**Upstream dependents to notify:** `api-accounts`, `api-dashboard`, `api-market` Also the Node API (`electric-wealth-api`, not in this workspace), which shares the database file.

**Downstream services to monitor post-change:** `db-identity-and-auth`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `e6262fcddca7f4c0488248ca8881483d9b920f0a` (2026-10-19)

**Open follow-ups from extraction:**

- `holdings.symbol` is not an FK to `securities` — referential drift is possible (Part A § 3.1).
