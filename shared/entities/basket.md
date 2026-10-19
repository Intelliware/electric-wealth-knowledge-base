# Entity: Basket

> **Entity ID:** `basket`
> **Owning component:** [`api-market`](../../components/electric-wealth-api-spring/api-market.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-market-data`](../../components/electric-wealth-db/db-market-data.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#baskets)); API shape: `Basket` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

A thematic basket of securities with risk level, description, 1-year return and holdings count. Neighbouring entities: [`Security`](../../shared/entities/security.md), `—`.

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-market`](../../components/electric-wealth-api-spring/api-market.md) |
| Repo | `api-spring` |
| Why this component owns it | Only reader. |
| Storage location (table / collection / index) | `baskets` in `data/electric-wealth.db` (schema: [`db-market-data`](../../components/electric-wealth-db/db-market-data.md)) |
| Identifier strategy | `baskets`: PK `(id)` — Text id |

> The schema is owned by `db-market-data` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `baskets.id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#baskets) | PK |
| `baskets.name` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#baskets) |  |
| `baskets.description` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#baskets) |  |
| `baskets.return_1y_pct` | REAL | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#baskets) | Percent |
| `baskets.holdings_count` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#baskets) |  |
| `baskets.risk` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#baskets) | `Low` \| `Medium` \| `High` |
| `baskets.sort_order` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#baskets) |  |

**Invariants:**

- Risk is Low / Medium / High.

**Validation hooks:**

- `MarketRepository` row mapper

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

**Cross-references:** components with `Basket` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
