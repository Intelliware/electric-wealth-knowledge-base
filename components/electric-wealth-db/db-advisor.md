---
component_id: db-advisor
repo: db
type: database-schema
description: >-
  Use when a change touches advisors, advisor appointments or secure advisor messages — including the partial unique slot index that prevents double booking.
triggers:
  - "advisors table"
  - "appointments"
  - "advisor messages"
  - "slot uniqueness"
  - "ux_appointments_advisor_slot"
  - "Toronto local time"
paths:
  - "migrations/005_advisor.sql"
  - "seed/105_advisor.sql"
depends_on:
  - "db-identity-and-auth"
consumed_by:
  - "api-advisor"
  - "db-identity-and-auth"
  - "db-online-enrolment"
data_entities:
  - "Advisor"
  - "Appointment"
  - "AdvisorMessage"
last_verified_against_commit: e6262fcddca7f4c0488248ca8881483d9b920f0a
last_feature_id: ""
source_doc: docs/database-internal/05-tables-detailed-documentation.md
verification_signal: schema-diff
---

# Advisor Schema

> **Repo:** `db` ([repo summary](../../repos/electric-wealth-db/README.md)) · **Type:** `database-schema` · **Source doc:** [`docs/database-internal/05-tables-detailed-documentation.md`](../../../electric-wealth-db/docs/database-internal/05-tables-detailed-documentation.md)

## 1. Overview

Introduced by migration 005. `advisors` is shared reference data used by `users`, `appointments`, `advisor_messages` and `offline_clients` (no delete action, so a referenced advisor cannot be deleted). `appointments.date` / `time` are Toronto local time — the one exception to the UTC convention.

The partial unique index `ux_appointments_advisor_slot` on `(advisor_id, date, time) WHERE status = 'scheduled'` is the concurrency guard behind the API's `SLOT_UNAVAILABLE` 409.

**Tables:** `advisors`, `appointments`, `advisor_messages`

**Depends on:** [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md)

**Consumed by:** [`api-advisor`](../../components/electric-wealth-api-spring/api-advisor.md), [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md), [`db-online-enrolment`](../../components/electric-wealth-db/db-online-enrolment.md)

**Shared entities:** [`Advisor`](../../shared/entities/advisor.md), [`Appointment`](../../shared/entities/appointment.md), [`AdvisorMessage`](../../shared/entities/advisor-message.md)

---

## 2. Key Responsibilities

- Define advisors, appointments (status lifecycle) and the message thread.
- Guarantee one scheduled appointment per advisor slot.

**Explicitly NOT in scope:**

- Slot generation and booking rules — owned by [`api-advisor`](../../components/electric-wealth-api-spring/api-advisor.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Tables `advisors`, `appointments`, `advisor_messages` | Direct SQL readers `api-advisor`, `db-identity-and-auth`, `db-online-enrolment`; Node API (`electric-wealth-api`) shares the same file | `schema-diff` (data dictionary vs `pragma_table_info`), then `./mvnw test` in `electric-wealth-api-spring` |
| Internal refactor (no interface change) | Seed data / indexes only | `npm run db:reset` then `npm run db:status` |
| Data-entity schema change | New migration required; every API repository touching these tables | Part A § 20 new-table checklist + API test suite |
| Dependency upgrade | Node `node:sqlite` / SQLite version affects all schema components | `npm run db:reset` + API test suite |

**Upstream dependents to notify:** `api-advisor`, `db-identity-and-auth`, `db-online-enrolment` Also the Node API (`electric-wealth-api`, not in this workspace), which shares the database file.

**Downstream services to monitor post-change:** `db-identity-and-auth`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `e6262fcddca7f4c0488248ca8881483d9b920f0a` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
