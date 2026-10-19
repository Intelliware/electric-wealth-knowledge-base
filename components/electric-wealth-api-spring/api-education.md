---
component_id: api-education
repo: api-spring
type: api-feature
description: >-
  Use when a client browses the learning catalogue, saves lesson progress or searches the financial glossary through /api/education.
triggers:
  - "education"
  - "learning centre"
  - "lessons"
  - "save progress"
  - "glossary"
  - "glossary search"
paths:
  - "src/main/java/com/electricwealth/api/web/EducationController.java"
  - "src/main/java/com/electricwealth/api/dto/EducationDtos.java"
  - "src/main/java/com/electricwealth/api/repository/EducationRepository.java"
  - "src/test/java/com/electricwealth/api/EducationAndMarketApiTest.java"
depends_on:
  - "db-learning-centre"
  - "api-request-pipeline"
  - "api-contract-and-data-access"
consumed_by:
  - "web-education"
data_entities:
  - "EducationContent"
  - "EducationProgress"
  - "GlossaryTerm"
last_verified_against_commit: 3f7403e33d97b39872e89690431c7880dea98ae4
last_feature_id: ""
source_doc: docs/api-internal/12-domain-models-and-business-rules.md
verification_signal: openapi-diff
---

# Education

> **Repo:** `api-spring` ([repo summary](../../repos/electric-wealth-api-spring/README.md)) · **Type:** `api-feature` · **Source doc:** [`docs/api-internal/12-domain-models-and-business-rules.md`](../../../electric-wealth-api-spring/docs/api-internal/12-domain-models-and-business-rules.md)

## 1. Overview

Serves bilingual catalogue and glossary text chosen by `Accept-Language`, filters by `level` / `type`, upserts progress 0–100, and searches the glossary with `LIKE` on term or definition, sorted in Java with a locale `Collator`.

**Operations:** `GET /api/education/content`, `GET /api/education/glossary`, `POST /api/education/progress`

**Depends on:** [`db-learning-centre`](../../components/electric-wealth-db/db-learning-centre.md), [`api-request-pipeline`](../../components/electric-wealth-api-spring/api-request-pipeline.md), [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md)

**Consumed by:** [`web-education`](../../components/electric-wealth-web/web-education.md)

**Shared entities:** [`EducationContent`](../../shared/entities/education-content.md), [`EducationProgress`](../../shared/entities/education-progress.md), [`GlossaryTerm`](../../shared/entities/glossary-term.md)

---

## 2. Key Responsibilities

- List lessons with the user's progress.
- Upsert progress.
- Search and sort the glossary per locale.

**Explicitly NOT in scope:**

- Lesson content authoring (seed only) — owned by [`db-learning-centre`](../../components/electric-wealth-db/db-learning-centre.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| HTTP operations (3) in `openapi.json`, tag `Education` | Web callers `web-education`; web `src/api/schema.d.ts` must be regenerated; Node API parity | `openapi-diff`: `OpenApiSpecTest`, `ContractParityTest`, then `npm run verify` in `electric-wealth-web` |
| Internal refactor (no interface change) | This component's controller / repository only | `./mvnw test -Dtest=EducationAndMarketApiTest` |
| Data-entity schema change | Schema owner(s) `db-learning-centre` and every API feature reading the same tables | `schema-diff` on `electric-wealth-db` + full `./mvnw test` |
| Dependency upgrade | Whole API (single Maven module) | `./mvnw verify` incl. `OpenApiSpecTest` |

**Upstream dependents to notify:** `web-education`

**Downstream services to monitor post-change:** `db-learning-centre`, `api-request-pipeline`, `api-contract-and-data-access`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
