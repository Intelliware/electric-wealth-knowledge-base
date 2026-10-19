# Entity: AssetAllocation

> **Entity ID:** `asset-allocation`
> **Owning component:** [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-goals-and-dashboard`](../../components/electric-wealth-db/db-goals-and-dashboard.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#asset_allocations)); API shape: `AllocationItem` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

One slice of the user's asset-allocation bar (asset class and weight). Neighbouring entities: [`PortfolioSnapshot`](../../shared/entities/portfolio-snapshot.md), `—`.

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md) |
| Repo | `api-spring` |
| Why this component owns it | Only reader. |
| Storage location (table / collection / index) | `asset_allocations` in `data/electric-wealth.db` (schema: [`db-goals-and-dashboard`](../../components/electric-wealth-db/db-goals-and-dashboard.md)) |
| Identifier strategy | `asset_allocations`: PK `(user_id, asset_class)` — One row per asset class per user |

> The schema is owned by `db-goals-and-dashboard` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `asset_allocations.user_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#asset_allocations) | PK1, FK→`users.id` (CASCADE) |
| `asset_allocations.asset_class` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#asset_allocations) | PK2; `Equity`, `Fixed Income`, `Cash`, `Alternatives` |
| `asset_allocations.pct` | REAL | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#asset_allocations) | Percent |
| `asset_allocations.sort_order` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#asset_allocations) |  |

**Invariants:**

- Weights are percentages.

**Validation hooks:**

- `asset_allocations` constraints

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Seeded by `db:seed` | [`db-migration-and-seed-tooling`](../../components/electric-wealth-db/db-migration-and-seed-tooling.md) | `npm run db:seed` / `db:reset` | Row present |
| Updated | — | — | No API operation updates this entity | — |
| Archived / Deleted | Hard delete via `ON DELETE CASCADE` from `users` | [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md) | User row deleted (no API operation does this) | Row removed |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`web-dashboard`](../../components/electric-wealth-web/web-dashboard.md) | `web` | `synchronous-read` | Calls `api-dashboard` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `AssetAllocation` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
