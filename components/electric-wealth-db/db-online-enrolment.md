---
component_id: db-online-enrolment
repo: db
type: database-schema
description: >-
  Use when a change touches online enrolment storage — branch client records, security questions, draft registrations, or the security answers saved during and after registration.
triggers:
  - "offline clients"
  - "registrations table"
  - "security questions"
  - "security answers"
  - "enrolled user"
  - "branch client"
paths:
  - "migrations/007_registration.sql"
  - "seed/107_registration.sql"
depends_on:
  - "db-identity-and-auth"
  - "db-advisor"
consumed_by:
  - "api-registration"
data_entities:
  - "OfflineClient"
  - "SecurityQuestion"
  - "Registration"
  - "SecurityAnswer"
last_verified_against_commit: e6262fcddca7f4c0488248ca8881483d9b920f0a
last_feature_id: ""
source_doc: docs/database-internal/05-tables-detailed-documentation.md
verification_signal: schema-diff
---

# Online Enrolment Schema

> **Repo:** `db` ([repo summary](../../repos/electric-wealth-db/README.md)) · **Type:** `database-schema` · **Source doc:** [`docs/database-internal/05-tables-detailed-documentation.md`](../../../electric-wealth-db/docs/database-internal/05-tables-detailed-documentation.md)

## 1. Overview

Introduced by migration 007. `offline_clients` are branch records that can enrol online (two demo clients); `registrations` hold multi-step drafts (30-minute TTL enforced by the API); answers are staged in `registration_security_answers` and copied to `user_security_answers` on completion.

`offline_clients.enrolled_user_id` and `registrations.user_id` reference `users` with NO ACTION, which blocks deleting an enrolled user.

**Tables:** `offline_clients`, `security_questions`, `registrations`, `registration_security_answers`, `user_security_answers`

**Depends on:** [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md), [`db-advisor`](../../components/electric-wealth-db/db-advisor.md)

**Consumed by:** [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md)

**Shared entities:** [`OfflineClient`](../../shared/entities/offline-client.md), [`SecurityQuestion`](../../shared/entities/security-question.md), [`Registration`](../../shared/entities/registration.md), [`SecurityAnswer`](../../shared/entities/security-answer.md)

---

## 2. Key Responsibilities

- Define branch-client identity data used for matching (account number, postal code, date of birth).
- Store draft registrations and normalised security answers.
- Provide the bilingual security-question catalogue.

**Explicitly NOT in scope:**

- Step ordering, TTL and identity matching — owned by [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Tables `offline_clients`, `security_questions`, `registrations`, `registration_security_answers`, `user_security_answers` | Direct SQL readers `api-registration`; Node API (`electric-wealth-api`) shares the same file | `schema-diff` (data dictionary vs `pragma_table_info`), then `./mvnw test` in `electric-wealth-api-spring` |
| Internal refactor (no interface change) | Seed data / indexes only | `npm run db:reset` then `npm run db:status` |
| Data-entity schema change | New migration required; every API repository touching these tables | Part A § 20 new-table checklist + API test suite |
| Dependency upgrade | Node `node:sqlite` / SQLite version affects all schema components | `npm run db:reset` + API test suite |

**Upstream dependents to notify:** `api-registration` Also the Node API (`electric-wealth-api`, not in this workspace), which shares the database file.

**Downstream services to monitor post-change:** `db-identity-and-auth`, `db-advisor`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `e6262fcddca7f4c0488248ca8881483d9b920f0a` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
