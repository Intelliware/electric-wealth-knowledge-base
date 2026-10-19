---
component_id: db-learning-centre
repo: db
type: database-schema
description: >-
  Use when a change touches education content, per-user learning progress or the bilingual financial glossary tables.
triggers:
  - "education content"
  - "education progress"
  - "glossary terms"
  - "bilingual columns"
  - "lesson level"
paths:
  - "migrations/004_education.sql"
  - "seed/104_education.sql"
depends_on:
  - "db-identity-and-auth"
consumed_by:
  - "api-education"
data_entities:
  - "EducationContent"
  - "EducationProgress"
  - "GlossaryTerm"
last_verified_against_commit: e6262fcddca7f4c0488248ca8881483d9b920f0a
last_feature_id: ""
source_doc: docs/database-internal/05-tables-detailed-documentation.md
verification_signal: schema-diff
---

# Learning Centre Schema

> **Repo:** `db` ([repo summary](../../repos/electric-wealth-db/README.md)) · **Type:** `database-schema` · **Source doc:** [`docs/database-internal/05-tables-detailed-documentation.md`](../../../electric-wealth-db/docs/database-internal/05-tables-detailed-documentation.md)

## 1. Overview

Introduced by migration 004. Catalogue text is bilingual through `_en` / `_fr` column pairs; the API picks one from `Accept-Language`. `education_progress` stores an INTEGER 0–100 percentage per (user, content) and is upserted by `api-education`. `glossary_terms` is standalone reference data.

**Tables:** `education_content`, `education_progress`, `glossary_terms`

**Depends on:** [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md)

**Consumed by:** [`api-education`](../../components/electric-wealth-api-spring/api-education.md)

**Shared entities:** [`EducationContent`](../../shared/entities/education-content.md), [`EducationProgress`](../../shared/entities/education-progress.md), [`GlossaryTerm`](../../shared/entities/glossary-term.md)

---

## 2. Key Responsibilities

- Define the bilingual lesson catalogue and glossary.
- Store per-user progress keyed by `(user_id, content_id)`.

**Explicitly NOT in scope:**

- Language selection and glossary collation — owned by [`api-education`](../../components/electric-wealth-api-spring/api-education.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Tables `education_content`, `education_progress`, `glossary_terms` | Direct SQL readers `api-education`; Node API (`electric-wealth-api`) shares the same file | `schema-diff` (data dictionary vs `pragma_table_info`), then `./mvnw test` in `electric-wealth-api-spring` |
| Internal refactor (no interface change) | Seed data / indexes only | `npm run db:reset` then `npm run db:status` |
| Data-entity schema change | New migration required; every API repository touching these tables | Part A § 20 new-table checklist + API test suite |
| Dependency upgrade | Node `node:sqlite` / SQLite version affects all schema components | `npm run db:reset` + API test suite |

**Upstream dependents to notify:** `api-education` Also the Node API (`electric-wealth-api`, not in this workspace), which shares the database file.

**Downstream services to monitor post-change:** `db-identity-and-auth`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `e6262fcddca7f4c0488248ca8881483d9b920f0a` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
