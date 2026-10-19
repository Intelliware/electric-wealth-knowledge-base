# Entity: SecurityQuestion

> **Entity ID:** `security-question`
> **Owning component:** [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-online-enrolment`](../../components/electric-wealth-db/db-online-enrolment.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#security_questions)); API shape: `SecurityQuestion` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

A bilingual security question offered during enrolment. Neighbouring entities: [`SecurityAnswer`](../../shared/entities/security-answer.md), `—`.

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md) |
| Repo | `api-spring` |
| Why this component owns it | Only reader. |
| Storage location (table / collection / index) | `security_questions` in `data/electric-wealth.db` (schema: [`db-online-enrolment`](../../components/electric-wealth-db/db-online-enrolment.md)) |
| Identifier strategy | `security_questions`: PK `(id)` — Text id (`q1`, `q2`, …) |

> The schema is owned by `db-online-enrolment` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `security_questions.id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#security_questions) | PK; `q1` … `q5` |
| `security_questions.text_en` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#security_questions) |  |
| `security_questions.text_fr` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#security_questions) |  |
| `security_questions.sort_order` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#security_questions) |  |

**Invariants:**

- Two different questions must be answered.

**Validation hooks:**

- `SecurityAnswersRequest` validation

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
| [`web-registration-wizard`](../../components/electric-wealth-web/web-registration-wizard.md) | `web` | `synchronous-read` | Calls `api-registration` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `SecurityQuestion` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
