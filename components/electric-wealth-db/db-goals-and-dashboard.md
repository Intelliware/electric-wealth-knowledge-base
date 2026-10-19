---
component_id: db-goals-and-dashboard
repo: db
type: database-schema
description: >-
  Use when a change touches savings goals or dashboard content tables — goals, portfolio_snapshots, asset_allocations, alerts or quick_links.
triggers:
  - "goals table"
  - "portfolio snapshot"
  - "asset allocation"
  - "dashboard alerts"
  - "quick links"
  - "show on dashboard"
  - "deep link column"
paths:
  - "migrations/001_initial_schema.sql"
  - "seed/00[1-6]_*.sql"
depends_on:
  - "db-identity-and-auth"
consumed_by:
  - "api-dashboard"
  - "api-goals"
  - "api-registration"
data_entities:
  - "Goal"
  - "PortfolioSnapshot"
  - "AssetAllocation"
  - "Alert"
  - "QuickLink"
last_verified_against_commit: e6262fcddca7f4c0488248ca8881483d9b920f0a
last_feature_id: ""
source_doc: docs/database-internal/05-tables-detailed-documentation.md
verification_signal: schema-diff
---

# Goals and Dashboard Schema

> **Repo:** `db` ([repo summary](../../repos/electric-wealth-db/README.md)) · **Type:** `database-schema` · **Source doc:** [`docs/database-internal/05-tables-detailed-documentation.md`](../../../electric-wealth-db/docs/database-internal/05-tables-detailed-documentation.md)

## 1. Overview

Backs the dashboard and the goals feature. `portfolio_snapshots` is 1:0..1 per user and deliberately does not store total value (the API sums visible account balances). `alerts` and `quick_links` carry mobile deep links (`electricwealth://…`).

Goals are written by `api-goals`; snapshots, quick links and the welcome data are also written by `api-registration` when a new user enrols. Allocations and alerts are seed-only.

**Tables:** `goals`, `portfolio_snapshots`, `asset_allocations`, `alerts`, `quick_links`

**Depends on:** [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md)

**Consumed by:** [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md), [`api-goals`](../../components/electric-wealth-api-spring/api-goals.md), [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md)

**Shared entities:** [`Goal`](../../shared/entities/goal.md), [`PortfolioSnapshot`](../../shared/entities/portfolio-snapshot.md), [`AssetAllocation`](../../shared/entities/asset-allocation.md), [`Alert`](../../shared/entities/alert.md), [`QuickLink`](../../shared/entities/quick-link.md)

---

## 2. Key Responsibilities

- Define goal rows (amounts in cents, target date, `show_on_dashboard`).
- Define the dashboard snapshot, allocation, alert and quick-link tables with per-user ordering.

**Explicitly NOT in scope:**

- Dashboard assembly and label translation — owned by [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md)
- Goal validation rules — owned by [`api-goals`](../../components/electric-wealth-api-spring/api-goals.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Tables `goals`, `portfolio_snapshots`, `asset_allocations`, `alerts`, `quick_links` | Direct SQL readers `api-dashboard`, `api-goals`, `api-registration`; Node API (`electric-wealth-api`) shares the same file | `schema-diff` (data dictionary vs `pragma_table_info`), then `./mvnw test` in `electric-wealth-api-spring` |
| Internal refactor (no interface change) | Seed data / indexes only | `npm run db:reset` then `npm run db:status` |
| Data-entity schema change | New migration required; every API repository touching these tables | Part A § 20 new-table checklist + API test suite |
| Dependency upgrade | Node `node:sqlite` / SQLite version affects all schema components | `npm run db:reset` + API test suite |

**Upstream dependents to notify:** `api-dashboard`, `api-goals`, `api-registration` Also the Node API (`electric-wealth-api`, not in this workspace), which shares the database file.

**Downstream services to monitor post-change:** `db-identity-and-auth`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `e6262fcddca7f4c0488248ca8881483d9b920f0a` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
