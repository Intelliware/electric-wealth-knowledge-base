# Entity: GlossaryTerm

> **Entity ID:** `glossary-term`
> **Owning component:** [`api-education`](../../components/electric-wealth-api-spring/api-education.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-learning-centre`](../../components/electric-wealth-db/db-learning-centre.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#glossary_terms)); API shape: `GlossaryEntry` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

A bilingual financial glossary term and definition. Neighbouring entities: [`EducationContent`](../../shared/entities/education-content.md), `—`.

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-education`](../../components/electric-wealth-api-spring/api-education.md) |
| Repo | `api-spring` |
| Why this component owns it | Only reader. |
| Storage location (table / collection / index) | `glossary_terms` in `data/electric-wealth.db` (schema: [`db-learning-centre`](../../components/electric-wealth-db/db-learning-centre.md)) |
| Identifier strategy | `glossary_terms`: PK `(id)` — Text id |

> The schema is owned by `db-learning-centre` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `glossary_terms.id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#glossary_terms) | PK; Slug |
| `glossary_terms.term_en` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#glossary_terms) |  |
| `glossary_terms.term_fr` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#glossary_terms) |  |
| `glossary_terms.definition_en` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#glossary_terms) |  |
| `glossary_terms.definition_fr` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#glossary_terms) |  |
| `glossary_terms.sort_order` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#glossary_terms) |  |

**Invariants:**

- Search uses `LIKE` on term or definition; sorting uses a locale `Collator` in Java.

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

**Cross-references:** components with `GlossaryTerm` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
