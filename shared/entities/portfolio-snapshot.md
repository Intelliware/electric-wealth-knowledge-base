# Entity: PortfolioSnapshot

> **Entity ID:** `portfolio-snapshot`
> **Owning component:** [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-goals-and-dashboard`](../../components/electric-wealth-db/db-goals-and-dashboard.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#portfolio_snapshots)); API shape: `PortfolioSummary`, `DashboardResponse` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

A user's dashboard snapshot: currency, day change and as-of time. Total value is deliberately not stored. Neighbouring entities: [`AssetAllocation`](../../shared/entities/asset-allocation.md), [`Account`](../../shared/entities/account.md).

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md) |
| Repo | `api-spring` |
| Why this component owns it | Reads and assembles the snapshot; registration only seeds the initial row. |
| Storage location (table / collection / index) | `portfolio_snapshots` in `data/electric-wealth.db` (schema: [`db-goals-and-dashboard`](../../components/electric-wealth-db/db-goals-and-dashboard.md)) |
| Identifier strategy | `portfolio_snapshots`: PK `(user_id)` — PK = `user_id` (1:0..1) |

> The schema is owned by `db-goals-and-dashboard` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `portfolio_snapshots.user_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#portfolio_snapshots) | PK, FK→`users.id` (CASCADE) |
| `portfolio_snapshots.currency` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#portfolio_snapshots) |  |
| `portfolio_snapshots.day_change` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#portfolio_snapshots) | Money |
| `portfolio_snapshots.day_change_pct` | REAL | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#portfolio_snapshots) | Percent |
| `portfolio_snapshots.as_of` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#portfolio_snapshots) | UTC |

**Invariants:**

- Dashboard returns 404 if absent.
- `totalValue` is computed from visible account balances.

**Validation hooks:**

- `DashboardRepository`

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Insert zero snapshot | [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md) | `POST /api/registration/complete` (or seed) | Snapshot exists |
| Updated | — | — | No API operation updates this entity | — |
| Archived / Deleted | Hard delete via `ON DELETE CASCADE` from `users` | [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md) | User row deleted (no API operation does this) | Row removed |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md) | `api-spring` | `direct-read` | Reads or writes the same tables through its own repository SQL (shared SQLite file, no service boundary) |
| [`web-dashboard`](../../components/electric-wealth-web/web-dashboard.md) | `web` | `synchronous-read` | Calls `api-dashboard` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `PortfolioSnapshot` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
