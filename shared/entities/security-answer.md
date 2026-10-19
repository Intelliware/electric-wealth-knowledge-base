# Entity: SecurityAnswer

> **Entity ID:** `security-answer`
> **Owning component:** [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-online-enrolment`](../../components/electric-wealth-db/db-online-enrolment.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#registration_security_answers)); API shape: `SecurityAnswer`, `SecurityAnswersRequest` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

A normalised answer to a security question — staged per registration, then copied to the new user on completion. Neighbouring entities: [`SecurityQuestion`](../../shared/entities/security-question.md), [`Registration`](../../shared/entities/registration.md).

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md) |
| Repo | `api-spring` |
| Why this component owns it | Only writer. |
| Storage location (table / collection / index) | `registration_security_answers`, `user_security_answers` in `data/electric-wealth.db` (schema: [`db-online-enrolment`](../../components/electric-wealth-db/db-online-enrolment.md)) |
| Identifier strategy | `registration_security_answers`: PK `(registration_id, question_id)`; `user_security_answers`: PK `(user_id, question_id)` — `(registration_id, question_id)` / `(user_id, question_id)` |

> The schema is owned by `db-online-enrolment` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `registration_security_answers.registration_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#registration_security_answers) | PK1, FK→`registrations.id` (CASCADE) |
| `registration_security_answers.question_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#registration_security_answers) | PK2, FK→`security_questions.id` (NO ACTION) |
| `registration_security_answers.answer` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#registration_security_answers) | Normalised (trimmed, lower-case) |
| `user_security_answers.user_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_security_answers) | PK1, FK→`users.id` (CASCADE) |
| `user_security_answers.question_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_security_answers) | PK2, FK→`security_questions.id` (NO ACTION) |
| `user_security_answers.answer` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_security_answers) | Normalised |

**Invariants:**

- Stored trimmed, lower-case, single-spaced.

**Validation hooks:**

- `RegistrationRepository`

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Insert staged answers (replacing earlier ones) | [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md) | `POST /api/registration/security-questions` | Staged |
| Updated | Copy to `user_security_answers` | [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md) | `POST /api/registration/complete` | Bound to user |
| Archived / Deleted | Cascade on registration / user delete | [`db-online-enrolment`](../../components/electric-wealth-db/db-online-enrolment.md) | — | Rows removed |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`web-registration-wizard`](../../components/electric-wealth-web/web-registration-wizard.md) | `web` | `synchronous-read` | Calls `api-registration` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `SecurityAnswer` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
