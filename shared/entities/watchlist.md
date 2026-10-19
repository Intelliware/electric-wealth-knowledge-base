# Entity: Watchlist

> **Entity ID:** `watchlist`
> **Owning component:** [`api-market`](../../components/electric-wealth-api-spring/api-market.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-market-data`](../../components/electric-wealth-db/db-market-data.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#watchlist)); API shape: `Quote (watched flag)` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

The ordered set of securities a user watches. Neighbouring entities: [`Security`](../../shared/entities/security.md), `—`.

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-market`](../../components/electric-wealth-api-spring/api-market.md) |
| Repo | `api-spring` |
| Why this component owns it | Only writer. |
| Storage location (table / collection / index) | `watchlist` in `data/electric-wealth.db` (schema: [`db-market-data`](../../components/electric-wealth-db/db-market-data.md)) |
| Identifier strategy | `watchlist`: PK `(user_id, symbol)` — PK `(user_id, symbol)` |

> The schema is owned by `db-market-data` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `watchlist.user_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#watchlist) | PK1, FK→`users.id` (CASCADE) |
| `watchlist.symbol` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#watchlist) | PK2, FK→`securities.symbol` (CASCADE) |
| `watchlist.added_at` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#watchlist) | UTC |

**Invariants:**

- Ordered by `added_at`.

**Validation hooks:**

- `ON CONFLICT DO NOTHING`

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Insert (idempotent) | [`api-market`](../../components/electric-wealth-api-spring/api-market.md) | `PUT /api/market/watchlist/{symbol}` | Watched |
| Updated | — | — | No API operation updates this entity | — |
| Archived / Deleted | Delete row (no-op if absent) | [`api-market`](../../components/electric-wealth-api-spring/api-market.md) | `DELETE /api/market/watchlist/{symbol}` | Not watched |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`web-market`](../../components/electric-wealth-web/web-market.md) | `web` | `synchronous-read` | Calls `api-market` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `Watchlist` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
