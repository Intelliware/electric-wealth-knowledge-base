# Entity: AccountPerformance

> **Entity ID:** `account-performance`
> **Owning component:** [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-accounts-and-investments`](../../components/electric-wealth-db/db-accounts-and-investments.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#account_performance)); API shape: `PerformanceResponse`, `PerformancePoint`, `Benchmark` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

An account's time-weighted return and its performance series against a benchmark (header row plus points). Neighbouring entities: [`Account`](../../shared/entities/account.md), `—`.

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md) |
| Repo | `api-spring` |
| Why this component owns it | Creates the header on account opening and serves the series. |
| Storage location (table / collection / index) | `account_performance`, `performance_points` in `data/electric-wealth.db` (schema: [`db-accounts-and-investments`](../../components/electric-wealth-db/db-accounts-and-investments.md)) |
| Identifier strategy | `account_performance`: PK `(user_id, account_id)`; `performance_points`: PK `(user_id, account_id, series, period)` — Header one per account; points keyed by `series` and `period` |

> The schema is owned by `db-accounts-and-investments` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `account_performance.user_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#account_performance) | PK1, FK→`accounts(user_id,id)` (CASCADE) |
| `account_performance.account_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#account_performance) | PK2, FK (composite) |
| `account_performance.time_weighted_return_pct` | REAL | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#account_performance) | Percent |
| `account_performance.currency` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#account_performance) |  |
| `account_performance.benchmark_name` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#account_performance) | e.g. `S&P/TSX Composite` |
| `performance_points.user_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#performance_points) | PK1, FK→`accounts(user_id,id)` (CASCADE) |
| `performance_points.account_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#performance_points) | PK2, FK (composite) |
| `performance_points.series` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#performance_points) | PK3; `account` (value = money) \| `benchmark` (value = index, start 100) |
| `performance_points.period` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#performance_points) | PK4; `YYYY-MM` |
| `performance_points.value` | REAL | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#performance_points) | See `series` |

**Invariants:**

- `range` query parameter does not filter the series (known gap).

**Validation hooks:**

- `AccountRepository.findPerformance`

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Insert empty header (benchmark "S&P/TSX Composite") | [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md) | `POST /api/wealth/accounts` | Header without points |
| Updated | — | — | No API operation updates this entity | — |
| Archived / Deleted | Cascade on account / user delete | [`db-accounts-and-investments`](../../components/electric-wealth-db/db-accounts-and-investments.md) | — | Rows removed |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`web-accounts`](../../components/electric-wealth-web/web-accounts.md) | `web` | `synchronous-read` | Calls `api-accounts` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `AccountPerformance` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
