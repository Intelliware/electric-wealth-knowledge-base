# Entity: PriceHistory

> **Entity ID:** `price-history`
> **Owning component:** [`api-market`](../../components/electric-wealth-api-spring/api-market.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-market-data`](../../components/electric-wealth-db/db-market-data.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#price_history)); API shape: `HistoryResponse`, `PricePoint`, `HistoryRange` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

Chart price points for a security per range: intraday times for `1D`, days for `1M`, months for `1Y` / `5Y`. Neighbouring entities: [`Security`](../../shared/entities/security.md), `—`.

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-market`](../../components/electric-wealth-api-spring/api-market.md) |
| Repo | `api-spring` |
| Why this component owns it | Only reader. |
| Storage location (table / collection / index) | `price_history` in `data/electric-wealth.db` (schema: [`db-market-data`](../../components/electric-wealth-db/db-market-data.md)) |
| Identifier strategy | `price_history`: PK `(symbol, range, t)` — One close per symbol, range and time label `t` |

> The schema is owned by `db-market-data` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `price_history.symbol` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#price_history) | PK1, FK→`securities.symbol` (CASCADE) |
| `price_history.range` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#price_history) | PK2; `1D` \| `1M` \| `1Y` \| `5Y` |
| `price_history.t` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#price_history) | PK3; `HH:MM` (1D), `YYYY-MM-DD` (1M), `YYYY-MM` (1Y/5Y) |
| `price_history.close` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#price_history) | Money |

**Invariants:**

- Largest seed table (408 rows).

**Validation hooks:**

- `MarketRepository`

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Seeded by `db:seed` | [`db-migration-and-seed-tooling`](../../components/electric-wealth-db/db-migration-and-seed-tooling.md) | `npm run db:seed` / `db:reset` | Row present |
| Updated | — | — | No API operation updates this entity | — |
| Archived / Deleted | Cascade on security delete | [`db-market-data`](../../components/electric-wealth-db/db-market-data.md) | — | Rows removed |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`web-market`](../../components/electric-wealth-web/web-market.md) | `web` | `synchronous-read` | Calls `api-market` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `PriceHistory` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
