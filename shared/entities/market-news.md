# Entity: MarketNews

> **Entity ID:** `market-news`
> **Owning component:** [`api-market`](../../components/electric-wealth-api-spring/api-market.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-market-data`](../../components/electric-wealth-db/db-market-data.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#market_news)); API shape: `NewsItem` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

A market headline with source and publish time. Neighbouring entities: [`MarketRoundupItem`](../../shared/entities/market-roundup-item.md), `—`.

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-market`](../../components/electric-wealth-api-spring/api-market.md) |
| Repo | `api-spring` |
| Why this component owns it | Only reader. |
| Storage location (table / collection / index) | `market_news` in `data/electric-wealth.db` (schema: [`db-market-data`](../../components/electric-wealth-db/db-market-data.md)) |
| Identifier strategy | `market_news`: PK `(id)` — Text id |

> The schema is owned by `db-market-data` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `market_news.id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#market_news) | PK |
| `market_news.title` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#market_news) |  |
| `market_news.source` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#market_news) | Publisher |
| `market_news.published_at` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#market_news) | UTC |
| `market_news.tag` | TEXT | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#market_news) | Topic |

**Invariants:**

- Returned newest first.

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

**Cross-references:** components with `MarketNews` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
