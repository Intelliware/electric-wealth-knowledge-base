# Entity: Transaction

> **Entity ID:** `transaction`
> **Owning component:** [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-accounts-and-investments`](../../components/electric-wealth-db/db-accounts-and-investments.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#transactions)); API shape: `Transaction`, `TransactionsResponse`, `TransactionType` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

A posted or scheduled money movement in an account (contribution, buy, dividend, …). Neighbouring entities: [`Account`](../../shared/entities/account.md), [`Holding`](../../shared/entities/holding.md).

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md) |
| Repo | `api-spring` |
| Why this component owns it | Only API component that reads transactions. |
| Storage location (table / collection / index) | `transactions` in `data/electric-wealth.db` (schema: [`db-accounts-and-investments`](../../components/electric-wealth-db/db-accounts-and-investments.md)) |
| Identifier strategy | `transactions`: PK `(user_id, account_id, id)` — Ids unique per account |

> The schema is owned by `db-accounts-and-investments` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `transactions.user_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#transactions) | PK1, FK→`accounts(user_id,id)` (CASCADE) |
| `transactions.account_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#transactions) | PK2, FK (composite) |
| `transactions.id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#transactions) | PK3; Unique per account |
| `transactions.date` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#transactions) | UTC timestamp (`2024-12-01T00:00:00Z`) |
| `transactions.description` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#transactions) |  |
| `transactions.detail` | TEXT | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#transactions) |  |
| `transactions.type` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#transactions) | `contribution` \| `buy` \| `sell` \| `dividend` \| `fee` \| `withdrawal` \| `transfer` \| `scheduled` |
| `transactions.amount` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#transactions) | Money, signed (negative = out) |
| `transactions.currency` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#transactions) |  |
| `transactions.status` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#transactions) | `posted` \| `scheduled` |

**Invariants:**

- Posted transactions are paged `date DESC, id`; scheduled ones are always returned in full.

**Validation hooks:**

- `AccountRepository`

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
| [`web-accounts`](../../components/electric-wealth-web/web-accounts.md) | `web` | `synchronous-read` | Calls `api-accounts` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `Transaction` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
