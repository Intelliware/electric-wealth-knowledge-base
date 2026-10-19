# Entity: Account

> **Entity ID:** `account`
> **Owning component:** [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-accounts-and-investments`](../../components/electric-wealth-db/db-accounts-and-investments.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#accounts)); API shape: `AccountSummary`, `AccountDetail`, `AccountOpenRequest`, `AccountPatchRequest` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

An investment account (TFSA, RRSP, FHSA, non-registered, …) owned by a user, with balance, day change, tax status, contribution room, nickname and visibility. Neighbouring entities: [`Holding`](../../shared/entities/holding.md), [`Transaction`](../../shared/entities/transaction.md).

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md) |
| Repo | `api-spring` |
| Why this component owns it | Owns account opening and all account writes. |
| Storage location (table / collection / index) | `accounts` in `data/electric-wealth.db` (schema: [`db-accounts-and-investments`](../../components/electric-wealth-db/db-accounts-and-investments.md)) |
| Identifier strategy | `accounts`: PK `(user_id, id)` — Composite `(user_id, id)`; seed ids like `tfsa`, new ids `acct-<8 hex>` |

> The schema is owned by `db-accounts-and-investments` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `accounts.user_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#accounts) | PK1, FK→`users.id` (CASCADE); Owner |
| `accounts.id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#accounts) | PK2; `tfsa`, `acct-<8 hex>` |
| `accounts.type` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#accounts) | `TFSA` \| `RRSP` \| `RESP` \| `RDSP` \| `FHSA` \| `NON_REGISTERED` \| `LIRA` \| `RRIF` |
| `accounts.display_name` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#accounts) |  |
| `accounts.nickname` | TEXT | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#accounts) | User label |
| `accounts.balance` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#accounts) | Money |
| `accounts.currency` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#accounts) |  |
| `accounts.hidden` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#accounts) | 0/1; hidden accounts are excluded from lists and the total |
| `accounts.day_change` | INTEGER | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#accounts) | Money |
| `accounts.day_change_pct` | REAL | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#accounts) | Percent |
| `accounts.contribution_room` | INTEGER | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#accounts) | Money; NULL = n/a |
| `accounts.tax_status` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#accounts) | `Tax-free` \| `Tax-deferred` \| `Taxable` (not enforced) |
| `accounts.rate_of_return` | REAL | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#accounts) | Percent |
| `accounts.sort_order` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#accounts) | Display order |

**Invariants:**

- Only `TFSA`, `RRSP`, `FHSA`, `NON_REGISTERED` can be opened online.
- Money in INTEGER cents with `currency`.

**Validation hooks:**

- `AccountController` / `OpenableAccountType`
- `accounts` CHECK constraints

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Insert account + empty performance row | [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md) | `POST /api/wealth/accounts` | Zero balance, CAD, appended to `sort_order` |
| Updated | Update nickname or `hidden` | [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md) | `PATCH /api/wealth/accounts/{id}` | Renamed / hidden |
| Archived / Deleted | Hard delete via `ON DELETE CASCADE` from `users` | [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md) | User row deleted (no API operation does this) | Row removed |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md) | `api-spring` | `direct-read` | Reads or writes the same tables through its own repository SQL (shared SQLite file, no service boundary) |
| [`web-accounts`](../../components/electric-wealth-web/web-accounts.md) | `web` | `synchronous-read` | Calls `api-accounts` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `Account` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
