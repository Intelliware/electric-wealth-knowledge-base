# Entity: EducationContent

> **Entity ID:** `education-content`
> **Owning component:** [`api-education`](../../components/electric-wealth-api-spring/api-education.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-learning-centre`](../../components/electric-wealth-db/db-learning-centre.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#education_content)); API shape: `EducationContent`, `EducationLevel`, `EducationType` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

A lesson, class or challenge in the learning centre with bilingual title, level, type and duration. Neighbouring entities: [`EducationProgress`](../../shared/entities/education-progress.md), [`GlossaryTerm`](../../shared/entities/glossary-term.md).

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-education`](../../components/electric-wealth-api-spring/api-education.md) |
| Repo | `api-spring` |
| Why this component owns it | Only reader. |
| Storage location (table / collection / index) | `education_content` in `data/electric-wealth.db` (schema: [`db-learning-centre`](../../components/electric-wealth-db/db-learning-centre.md)) |
| Identifier strategy | `education_content`: PK `(id)` — Text id |

> The schema is owned by `db-learning-centre` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `education_content.id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#education_content) | PK; `e1` … |
| `education_content.title_en` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#education_content) |  |
| `education_content.title_fr` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#education_content) |  |
| `education_content.level` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#education_content) | `beginner` \| `intermediate` \| `advanced` |
| `education_content.type` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#education_content) | `class` \| `challenge` |
| `education_content.duration_min` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#education_content) | Minutes, `> 0` |
| `education_content.sort_order` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#education_content) |  |

**Invariants:**

- Text columns come in `_en` / `_fr` pairs.

**Validation hooks:**

- `EducationRepository`

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
| [`web-education`](../../components/electric-wealth-web/web-education.md) | `web` | `synchronous-read` | Calls `api-education` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `EducationContent` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
