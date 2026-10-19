---
component_id: api-advisor
repo: api-spring
type: api-feature
description: >-
  Use when a client checks advisor availability, books, reschedules or cancels an appointment, or reads and sends secure messages through /api/advisor.
triggers:
  - "advisor"
  - "availability"
  - "book appointment"
  - "reschedule"
  - "cancel appointment"
  - "secure messages"
  - "SLOT_UNAVAILABLE"
  - "NO_ADVISOR"
paths:
  - "src/main/java/com/electricwealth/api/web/AdvisorController.java"
  - "src/main/java/com/electricwealth/api/dto/AdvisorDtos.java"
  - "src/main/java/com/electricwealth/api/repository/AdvisorRepository.java"
  - "src/test/java/com/electricwealth/api/AdvisorApiTest.java"
depends_on:
  - "db-advisor"
  - "db-identity-and-auth"
  - "api-request-pipeline"
  - "api-contract-and-data-access"
consumed_by:
  - "web-advisor"
data_entities:
  - "Advisor"
  - "Appointment"
  - "AdvisorMessage"
  - "User"
last_verified_against_commit: 3f7403e33d97b39872e89690431c7880dea98ae4
last_feature_id: ""
source_doc: docs/api-internal/12-domain-models-and-business-rules.md
verification_signal: openapi-diff
---

# Advisor Booking and Messaging

> **Repo:** `api-spring` ([repo summary](../../repos/electric-wealth-api-spring/README.md)) · **Type:** `api-feature` · **Source doc:** [`docs/api-internal/12-domain-models-and-business-rules.md`](../../../electric-wealth-api-spring/docs/api-internal/12-domain-models-and-business-rules.md)

## 1. Overview

Generates slots `09:00, 10:30, 12:00, 14:30, 16:00` (Toronto time) on the next five weekdays starting tomorrow; a slot is open if no `scheduled` appointment holds it. Booking or rescheduling outside the window or losing a race on `ux_appointments_advisor_slot` returns 409 `SLOT_UNAVAILABLE`; only `scheduled` appointments can change (`NOT_SCHEDULED`). A user without an assigned advisor gets 404 `NO_ADVISOR`.

Messages are a single thread with the assigned advisor, oldest first; the API inserts client-sent messages only.

**Operations:** `GET /api/advisor/availability`, `GET /api/advisor/appointments`, `POST /api/advisor/appointments`, `PATCH /api/advisor/appointments/{id}`, `GET /api/advisor/messages`, `POST /api/advisor/messages`

**Depends on:** [`db-advisor`](../../components/electric-wealth-db/db-advisor.md), [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md), [`api-request-pipeline`](../../components/electric-wealth-api-spring/api-request-pipeline.md), [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md)

**Consumed by:** [`web-advisor`](../../components/electric-wealth-web/web-advisor.md)

**Shared entities:** [`Advisor`](../../shared/entities/advisor.md), [`Appointment`](../../shared/entities/appointment.md), [`AdvisorMessage`](../../shared/entities/advisor-message.md), [`User`](../../shared/entities/user.md)

---

## 2. Key Responsibilities

- Compute availability.
- Book, reschedule and cancel appointments with slot-conflict handling.
- Read and append to the secure message thread.

**Explicitly NOT in scope:**

- Default meeting type preference — owned by [`api-profile-and-preferences`](../../components/electric-wealth-api-spring/api-profile-and-preferences.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| HTTP operations (6) in `openapi.json`, tag `Advisor` | Web callers `web-advisor`; web `src/api/schema.d.ts` must be regenerated; Node API parity | `openapi-diff`: `OpenApiSpecTest`, `ContractParityTest`, then `npm run verify` in `electric-wealth-web` |
| Internal refactor (no interface change) | This component's controller / repository only | `./mvnw test -Dtest=AdvisorApiTest` |
| Data-entity schema change | Schema owner(s) `db-advisor`, `db-identity-and-auth` and every API feature reading the same tables | `schema-diff` on `electric-wealth-db` + full `./mvnw test` |
| Dependency upgrade | Whole API (single Maven module) | `./mvnw verify` incl. `OpenApiSpecTest` |

**Upstream dependents to notify:** `web-advisor`

**Downstream services to monitor post-change:** `db-advisor`, `db-identity-and-auth`, `api-request-pipeline`, `api-contract-and-data-access`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (2026-10-19)

**Open follow-ups from extraction:**

- `meetingType` on availability is accepted and ignored; no holiday calendar (Part A § 12.4).
