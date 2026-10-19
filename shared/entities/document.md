# Entity: Document

> **Entity ID:** `document`
> **Owning component:** [`api-documents`](../../components/electric-wealth-api-spring/api-documents.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-documents`](../../components/electric-wealth-db/db-documents.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#documents)); API shape: `Document`, `DocumentsListResponse`, `DocumentDetailResponse` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

Metadata for a client document (statement, tax slip, confirmation, legal) with year, category, size and action-required flag. No binary is stored. Neighbouring entities: [`DocumentShare`](../../shared/entities/document-share.md), [`Alert`](../../shared/entities/alert.md).

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-documents`](../../components/electric-wealth-api-spring/api-documents.md) |
| Repo | `api-spring` |
| Why this component owns it | Only API component that reads documents. |
| Storage location (table / collection / index) | `documents` in `data/electric-wealth.db` (schema: [`db-documents`](../../components/electric-wealth-db/db-documents.md)) |
| Identifier strategy | `documents`: PK `(user_id, id)` — Composite `(user_id, id)`; seed ids like `d1` |

> The schema is owned by `db-documents` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `documents.user_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#documents) | PK1, FK→`users.id` (CASCADE) |
| `documents.id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#documents) | PK2; `d1` … |
| `documents.name` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#documents) |  |
| `documents.category` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#documents) | `Tax` \| `Statements` \| `Confirmations` \| `Legal` (not enforced) |
| `documents.year` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#documents) |  |
| `documents.account_id` | TEXT | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#documents) | Related account (**no FK**); NULL = all accounts |
| `documents.account_label` | TEXT | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#documents) | Denormalised account name |
| `documents.size_bytes` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#documents) |  |
| `documents.action_required` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#documents) | 0/1 |
| `documents.url` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#documents) | `mock://signed/<id>` |

**Invariants:**

- Listed `year DESC, action_required DESC, id`.

**Validation hooks:**

- `DocumentRepository`

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Seeded by `db:seed` | [`db-migration-and-seed-tooling`](../../components/electric-wealth-db/db-migration-and-seed-tooling.md) | `npm run db:seed` / `db:reset` | Row present |
| Updated | — | — | No API operation updates this entity | — |
| Archived / Deleted | Hard delete via `ON DELETE CASCADE` from `users` | [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md) | User row deleted (no API operation does this) | Row removed |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`web-documents`](../../components/electric-wealth-web/web-documents.md) | `web` | `synchronous-read` | Calls `api-documents` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `Document` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
