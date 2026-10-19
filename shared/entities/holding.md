# Entity: Holding

> **Entity ID:** `holding`
> **Owning component:** [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-accounts-and-investments`](../../components/electric-wealth-db/db-accounts-and-investments.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#holdings)); API shape: `Holding` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

A position in a security inside an account: symbol, name, quantity, market value and portfolio weight. Neighbouring entities: [`Account`](../../shared/entities/account.md), [`Security`](../../shared/entities/security.md).

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md) |
| Repo | `api-spring` |
| Why this component owns it | Returned in account detail; `api-market` reads it only for the `held` flag. |
| Storage location (table / collection / index) | `holdings` in `data/electric-wealth.db` (schema: [`db-accounts-and-investments`](../../components/electric-wealth-db/db-accounts-and-investments.md)) |
| Identifier strategy | `holdings`: PK `(user_id, account_id, symbol)` — One row per symbol per account |

> The schema is owned by `db-accounts-and-investments` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `holdings.user_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#holdings) | PK1, FK→`accounts(user_id,id)` (CASCADE) |
| `holdings.account_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#holdings) | PK2, FK (composite) |
| `holdings.symbol` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#holdings) | PK3; Ticker (no FK to `securities`) |
| `holdings.name` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#holdings) | Security name |
| `holdings.quantity` | REAL | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#holdings) | Units |
| `holdings.market_value` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#holdings) | Money |
| `holdings.currency` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#holdings) |  |
| `holdings.weight_pct` | REAL | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#holdings) | Percent of the account |

**Invariants:**

- `symbol` is free text — not an FK to `securities`.

**Validation hooks:**

- `holdings` constraints

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Seeded by `db:seed` | [`db-migration-and-seed-tooling`](../../components/electric-wealth-db/db-migration-and-seed-tooling.md) | `npm run db:seed` / `db:reset` | Row present |
| Updated | — | — | No API operation updates this entity | — |
| Archived / Deleted | Cascade on account / user delete | [`db-accounts-and-investments`](../../components/electric-wealth-db/db-accounts-and-investments.md) | — | Row removed |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`api-market`](../../components/electric-wealth-api-spring/api-market.md) | `api-spring` | `direct-read` | Reads or writes the same tables through its own repository SQL (shared SQLite file, no service boundary) |
| [`web-accounts`](../../components/electric-wealth-web/web-accounts.md) | `web` | `synchronous-read` | Calls `api-accounts` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `Holding` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
