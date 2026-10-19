# Entity: DocumentShare

> **Entity ID:** `document-share`
> **Owning component:** [`api-documents`](../../components/electric-wealth-api-spring/api-documents.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-documents`](../../components/electric-wealth-db/db-documents.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#document_shares)); API shape: `DocumentShareRequest`, `DocumentShareResponse` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

A record that a document was shared with an email recipient, expiring after 7 days. Neighbouring entities: [`Document`](../../shared/entities/document.md), `—`.

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-documents`](../../components/electric-wealth-api-spring/api-documents.md) |
| Repo | `api-spring` |
| Why this component owns it | Only writer. |
| Storage location (table / collection / index) | `document_shares` in `data/electric-wealth.db` (schema: [`db-documents`](../../components/electric-wealth-db/db-documents.md)) |
| Identifier strategy | `document_shares`: PK `(id)` — INTEGER AUTOINCREMENT id |

> The schema is owned by `db-documents` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `document_shares.id` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#document_shares) | PK AUTOINCREMENT; Surrogate id |
| `document_shares.user_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#document_shares) | FK→`documents(user_id,id)` (CASCADE) |
| `document_shares.document_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#document_shares) | FK (composite) |
| `document_shares.recipient` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#document_shares) | Recipient email |
| `document_shares.created_at` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#document_shares) |  |
| `document_shares.expires_at` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#document_shares) | Created + 7 days |

**Invariants:**

- No email is actually sent.

**Validation hooks:**

- `DocumentRepository.SHARE_TTL`

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Insert share row | [`api-documents`](../../components/electric-wealth-api-spring/api-documents.md) | `POST /api/wealth/documents/{id}/share` | Expires in 7 days |
| Updated | — | — | No API operation updates this entity | — |
| Archived / Deleted | Cascade on document delete | [`db-documents`](../../components/electric-wealth-db/db-documents.md) | — | Row removed |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`web-documents`](../../components/electric-wealth-web/web-documents.md) | `web` | `synchronous-read` | Calls `api-documents` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `DocumentShare` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
