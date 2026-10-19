# Entity: Goal

> **Entity ID:** `goal`
> **Owning component:** [`api-goals`](../../components/electric-wealth-api-spring/api-goals.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-goals-and-dashboard`](../../components/electric-wealth-db/db-goals-and-dashboard.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#goals)); API shape: `Goal`, `GoalCreateRequest`, `GoalsListResponse`, `DashboardGoalSummary` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

A savings goal with name, category, current and target amounts, target date and whether it appears on the dashboard. Neighbouring entities: [`PortfolioSnapshot`](../../shared/entities/portfolio-snapshot.md), `—`.

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-goals`](../../components/electric-wealth-api-spring/api-goals.md) |
| Repo | `api-spring` |
| Why this component owns it | Only component that creates goals. |
| Storage location (table / collection / index) | `goals` in `data/electric-wealth.db` (schema: [`db-goals-and-dashboard`](../../components/electric-wealth-db/db-goals-and-dashboard.md)) |
| Identifier strategy | `goals`: PK `(user_id, id)` — Composite `(user_id, id)`; new ids `goal-<8 hex>` |

> The schema is owned by `db-goals-and-dashboard` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `goals.user_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#goals) | PK1, FK→`users.id` (CASCADE) |
| `goals.id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#goals) | PK2 |
| `goals.name` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#goals) |  |
| `goals.category` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#goals) | `Education` \| `Vacation` \| `Home` \| `Retirement` \| `Safety` |
| `goals.current_amount` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#goals) | Money |
| `goals.target_amount` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#goals) | Money |
| `goals.currency` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#goals) |  |
| `goals.target_date` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#goals) | `YYYY-MM-DD` |
| `goals.progress_pct` | REAL | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#goals) | Stored percent |
| `goals.show_on_dashboard` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#goals) | 0/1 |
| `goals.sort_order` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#goals) |  |

**Invariants:**

- `targetDate` after today (UTC); `currentAmount` ≤ `targetAmount` ≤ $100M; currency `CAD`.

**Validation hooks:**

- `GoalController` validation
- `GoalDtos.GoalCreateRequest`

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Insert goal | [`api-goals`](../../components/electric-wealth-api-spring/api-goals.md) | `POST /api/wealth/goals` | Not shown on dashboard |
| Updated | — | — | No API operation updates this entity | — |
| Archived / Deleted | Hard delete via `ON DELETE CASCADE` from `users` | [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md) | User row deleted (no API operation does this) | Row removed |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md) | `api-spring` | `direct-read` | Reads or writes the same tables through its own repository SQL (shared SQLite file, no service boundary) |
| [`web-dashboard`](../../components/electric-wealth-web/web-dashboard.md) | `web` | `synchronous-read` | Calls `api-goals` over HTTP (via `web-api-client`) to render it |
| [`web-goals`](../../components/electric-wealth-web/web-goals.md) | `web` | `synchronous-read` | Calls `api-goals` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `Goal` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
