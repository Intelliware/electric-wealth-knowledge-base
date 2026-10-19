# Entity: MarketRoundupItem

> **Entity ID:** `market-roundup-item`
> **Owning component:** [`api-market`](../../components/electric-wealth-api-spring/api-market.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-market-data`](../../components/electric-wealth-db/db-market-data.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#market_roundup_items)); API shape: `RoundupResponse`, `RoundupItem` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

One item in the weekly market roundup, grouped by `week_of`. Neighbouring entities: [`MarketNews`](../../shared/entities/market-news.md), `—`.

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-market`](../../components/electric-wealth-api-spring/api-market.md) |
| Repo | `api-spring` |
| Why this component owns it | Only reader. |
| Storage location (table / collection / index) | `market_roundup_items` in `data/electric-wealth.db` (schema: [`db-market-data`](../../components/electric-wealth-db/db-market-data.md)) |
| Identifier strategy | `market_roundup_items`: PK `(id)` — Text id; grouped by `week_of` |

> The schema is owned by `db-market-data` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `market_roundup_items.id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#market_roundup_items) | PK |
| `market_roundup_items.week_of` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#market_roundup_items) | `YYYY-MM-DD` of the covered week |
| `market_roundup_items.title` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#market_roundup_items) |  |
| `market_roundup_items.summary` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#market_roundup_items) |  |
| `market_roundup_items.published_at` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#market_roundup_items) | UTC |

**Invariants:**

- Only the latest `week_of` is returned.

**Validation hooks:**

- `MarketRepository`

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Seeded by `db:seed` | [`db-migration-and-seed-tooling`](../../components/electric-wealth-db/db-migration-and-seed-tooling.md) | `npm run db:seed` / `db:reset` | Row present |
| Updated | — | — | No API operation updates this entity | — |
| Archived / Deleted | Static reference data; no API operation deletes it | — | — | — |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`web-market`](../../components/electric-wealth-web/web-market.md) | `web` | `synchronous-read` | Calls `api-market` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `MarketRoundupItem` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
