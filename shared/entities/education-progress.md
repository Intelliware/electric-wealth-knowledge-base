# Entity: EducationProgress

> **Entity ID:** `education-progress`
> **Owning component:** [`api-education`](../../components/electric-wealth-api-spring/api-education.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-learning-centre`](../../components/electric-wealth-db/db-learning-centre.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#education_progress)); API shape: `EducationProgressRequest`, `EducationProgressResponse` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

A user's completion percentage (0–100) for one content item. Neighbouring entities: [`EducationContent`](../../shared/entities/education-content.md), `—`.

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-education`](../../components/electric-wealth-api-spring/api-education.md) |
| Repo | `api-spring` |
| Why this component owns it | Only writer. |
| Storage location (table / collection / index) | `education_progress` in `data/electric-wealth.db` (schema: [`db-learning-centre`](../../components/electric-wealth-db/db-learning-centre.md)) |
| Identifier strategy | `education_progress`: PK `(user_id, content_id)` — PK `(user_id, content_id)` |

> The schema is owned by `db-learning-centre` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `education_progress.user_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#education_progress) | PK1, FK→`users.id` (CASCADE) |
| `education_progress.content_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#education_progress) | PK2, FK→`education_content.id` (CASCADE) |
| `education_progress.progress_pct` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#education_progress) | 0–100 |
| `education_progress.updated_at` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#education_progress) | UTC |

**Invariants:**

- `progress_pct` INTEGER 0–100.

**Validation hooks:**

- `EducationRepository` upsert
- CHECK constraint

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Upsert progress | [`api-education`](../../components/electric-wealth-api-spring/api-education.md) | `POST /api/education/progress` | Progress stored |
| Updated | Upsert progress | [`api-education`](../../components/electric-wealth-api-spring/api-education.md) | `POST /api/education/progress` | Progress replaced |
| Archived / Deleted | Hard delete via `ON DELETE CASCADE` from `users` | [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md) | User row deleted (no API operation does this) | Row removed |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`web-education`](../../components/electric-wealth-web/web-education.md) | `web` | `synchronous-read` | Calls `api-education` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `EducationProgress` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
