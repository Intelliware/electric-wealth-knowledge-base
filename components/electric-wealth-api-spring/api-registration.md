---
component_id: api-registration
repo: api-spring
type: api-feature
description: >-
  Use when an existing branch client enrols for online access through the multi-step /api/registration flow — login choice, identity match, security questions, optional trusted contact and completion.
triggers:
  - "register"
  - "online enrolment"
  - "verify identity"
  - "security questions"
  - "trusted contact"
  - "complete registration"
  - "EMAIL_TAKEN"
  - "IDENTITY_MISMATCH"
  - "STEP_OUT_OF_ORDER"
paths:
  - "src/main/java/com/electricwealth/api/web/RegistrationController.java"
  - "src/main/java/com/electricwealth/api/dto/RegistrationDtos.java"
  - "src/main/java/com/electricwealth/api/repository/RegistrationRepository.java"
  - "src/test/java/com/electricwealth/api/RegistrationApiTest.java"
depends_on:
  - "api-auth-sessions"
  - "db-online-enrolment"
  - "db-identity-and-auth"
  - "db-goals-and-dashboard"
  - "db-notifications"
  - "api-request-pipeline"
  - "api-contract-and-data-access"
consumed_by:
  - "web-registration-wizard"
data_entities:
  - "Registration"
  - "OfflineClient"
  - "SecurityQuestion"
  - "SecurityAnswer"
  - "User"
  - "UserProfile"
  - "UserPreferences"
  - "Session"
  - "PortfolioSnapshot"
  - "Notification"
  - "QuickLink"
last_verified_against_commit: 3f7403e33d97b39872e89690431c7880dea98ae4
last_feature_id: ""
source_doc: docs/api-internal/12-domain-models-and-business-rules.md
verification_signal: openapi-diff
---

# Online Registration

> **Repo:** `api-spring` ([repo summary](../../repos/electric-wealth-api-spring/README.md)) · **Type:** `api-feature` · **Source doc:** [`docs/api-internal/12-domain-models-and-business-rules.md`](../../../electric-wealth-api-spring/docs/api-internal/12-domain-models-and-business-rules.md)

## 1. Overview

Drives a server-held draft (`registrations`, 30-minute TTL) through start → verify-identity → security-questions → optional trusted-contact → complete. Identity matches an `offline_clients` row by 10-digit account number, postal code (spaces/case ignored) and exact date of birth.

Completion is the widest write in the system: in one transaction it creates the user, profile (copied from the branch record), preferences, security answers, a zero portfolio snapshot, a localised welcome notification and three quick links, marks the branch client enrolled, and returns an already MFA-verified session via `SessionRepository`.

**Operations:** `POST /api/registration/start`, `POST /api/registration/verify-identity`, `GET /api/registration/security-questions`, `POST /api/registration/security-questions`, `POST /api/registration/trusted-contact`, `POST /api/registration/complete`

**Depends on:** [`api-auth-sessions`](../../components/electric-wealth-api-spring/api-auth-sessions.md), [`db-online-enrolment`](../../components/electric-wealth-db/db-online-enrolment.md), [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md), [`db-goals-and-dashboard`](../../components/electric-wealth-db/db-goals-and-dashboard.md), [`db-notifications`](../../components/electric-wealth-db/db-notifications.md), [`api-request-pipeline`](../../components/electric-wealth-api-spring/api-request-pipeline.md), [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md)

**Consumed by:** [`web-registration-wizard`](../../components/electric-wealth-web/web-registration-wizard.md)

**Shared entities:** [`Registration`](../../shared/entities/registration.md), [`OfflineClient`](../../shared/entities/offline-client.md), [`SecurityQuestion`](../../shared/entities/security-question.md), [`SecurityAnswer`](../../shared/entities/security-answer.md), [`User`](../../shared/entities/user.md), [`UserProfile`](../../shared/entities/user-profile.md), [`UserPreferences`](../../shared/entities/user-preferences.md), [`Session`](../../shared/entities/session.md), [`PortfolioSnapshot`](../../shared/entities/portfolio-snapshot.md), [`Notification`](../../shared/entities/notification.md), [`QuickLink`](../../shared/entities/quick-link.md)

---

## 2. Key Responsibilities

- Enforce step order (`STEP_OUT_OF_ORDER`) and draft expiry (`REGISTRATION_NOT_FOUND`).
- Match branch-client identity and block double enrolment (`ALREADY_ENROLLED`, `EMAIL_TAKEN`).
- Store normalised security answers.
- Provision a complete new user and return a signed-in session.

**Explicitly NOT in scope:**

- Subsequent sign-ins — owned by [`api-auth-sessions`](../../components/electric-wealth-api-spring/api-auth-sessions.md)
- Dashboard rendering of the provisioned data — owned by [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| HTTP operations (6) in `openapi.json`, tag `Registration` | Web callers `web-registration-wizard`; web `src/api/schema.d.ts` must be regenerated; Node API parity | `openapi-diff`: `OpenApiSpecTest`, `ContractParityTest`, then `npm run verify` in `electric-wealth-web` |
| Internal refactor (no interface change) | This component's controller / repository only | `./mvnw test -Dtest=RegistrationApiTest` |
| Data-entity schema change | Schema owner(s) `db-online-enrolment`, `db-identity-and-auth`, `db-goals-and-dashboard`, `db-notifications` and every API feature reading the same tables | `schema-diff` on `electric-wealth-db` + full `./mvnw test` |
| Dependency upgrade | Whole API (single Maven module) | `./mvnw verify` incl. `OpenApiSpecTest` |

**Upstream dependents to notify:** `web-registration-wizard`

**Downstream services to monitor post-change:** `api-auth-sessions`, `db-online-enrolment`, `db-identity-and-auth`, `db-goals-and-dashboard`, `db-notifications`, `api-request-pipeline`, `api-contract-and-data-access`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
