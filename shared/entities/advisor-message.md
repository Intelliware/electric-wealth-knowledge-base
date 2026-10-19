# Entity: AdvisorMessage

> **Entity ID:** `advisor-message`
> **Owning component:** [`api-advisor`](../../components/electric-wealth-api-spring/api-advisor.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-advisor`](../../components/electric-wealth-db/db-advisor.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#advisor_messages)); API shape: `AdvisorMessage`, `AdvisorMessageCreateRequest` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

One message in the secure thread between a user and their advisor. Neighbouring entities: [`Advisor`](../../shared/entities/advisor.md), `—`.

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-advisor`](../../components/electric-wealth-api-spring/api-advisor.md) |
| Repo | `api-spring` |
| Why this component owns it | Only writer. |
| Storage location (table / collection / index) | `advisor_messages` in `data/electric-wealth.db` (schema: [`db-advisor`](../../components/electric-wealth-db/db-advisor.md)) |
| Identifier strategy | `advisor_messages`: PK `(id)` — `msg-<8 hex>` |

> The schema is owned by `db-advisor` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `advisor_messages.id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#advisor_messages) | PK; `msg-<8 hex>` |
| `advisor_messages.user_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#advisor_messages) | FK→`users.id` (CASCADE); Client |
| `advisor_messages.advisor_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#advisor_messages) | FK→`advisors.id` (NO ACTION) |
| `advisor_messages.sender` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#advisor_messages) | `advisor` \| `client` |
| `advisor_messages.text` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#advisor_messages) |  |
| `advisor_messages.sent_at` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#advisor_messages) | UTC |

**Invariants:**

- Thread returned oldest first.

**Validation hooks:**

- `AdvisorDtos` validation

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Insert client message (advisor replies exist only in seed) | [`api-advisor`](../../components/electric-wealth-api-spring/api-advisor.md) | `POST /api/advisor/messages` | Appended to thread |
| Updated | — | — | No API operation updates this entity | — |
| Archived / Deleted | Hard delete via `ON DELETE CASCADE` from `users` | [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md) | User row deleted (no API operation does this) | Row removed |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`web-advisor`](../../components/electric-wealth-web/web-advisor.md) | `web` | `synchronous-read` | Calls `api-advisor` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `AdvisorMessage` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
