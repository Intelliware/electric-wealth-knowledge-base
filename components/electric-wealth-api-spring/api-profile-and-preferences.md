---
component_id: api-profile-and-preferences
repo: api-spring
type: api-feature
description: >-
  Use when a client reads their profile, replaces their mailing address, or reads or partially updates preferences (theme, language, notification channels, document delivery, default meeting type).
triggers:
  - "profile"
  - "mailing address"
  - "update address"
  - "preferences"
  - "theme"
  - "language preference"
  - "notification channels"
  - "document delivery"
  - "default meeting type"
paths:
  - "src/main/java/com/electricwealth/api/web/ProfileController.java"
  - "src/main/java/com/electricwealth/api/dto/ProfileDtos.java"
  - "src/main/java/com/electricwealth/api/repository/ProfileRepository.java"
  - "src/test/java/com/electricwealth/api/ProfileApiTest.java"
depends_on:
  - "db-identity-and-auth"
  - "api-request-pipeline"
  - "api-contract-and-data-access"
consumed_by:
  - "web-advisor"
  - "web-dashboard"
  - "web-profile"
  - "web-settings"
data_entities:
  - "User"
  - "UserProfile"
  - "UserPreferences"
last_verified_against_commit: 3f7403e33d97b39872e89690431c7880dea98ae4
last_feature_id: ""
source_doc: docs/api-internal/12-domain-models-and-business-rules.md
verification_signal: openapi-diff
---

# Profile and Preferences

> **Repo:** `api-spring` ([repo summary](../../repos/electric-wealth-api-spring/README.md)) · **Type:** `api-feature` · **Source doc:** [`docs/api-internal/12-domain-models-and-business-rules.md`](../../../electric-wealth-api-spring/docs/api-internal/12-domain-models-and-business-rules.md)

## 1. Overview

Serves the profile (masked phone/SIN, address, trusted contact) and preferences. Address update replaces all fields, normalises the postal code to `A1A 1A1`, stores blank `line2` as NULL and accepts only `Canada`. Preferences update is a partial merge, including individual notification flags.

**Operations:** `GET /api/profile`, `PUT /api/profile/address`, `GET /api/preferences`, `PUT /api/preferences`

**Depends on:** [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md), [`api-request-pipeline`](../../components/electric-wealth-api-spring/api-request-pipeline.md), [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md)

**Consumed by:** [`web-advisor`](../../components/electric-wealth-web/web-advisor.md), [`web-dashboard`](../../components/electric-wealth-web/web-dashboard.md), [`web-profile`](../../components/electric-wealth-web/web-profile.md), [`web-settings`](../../components/electric-wealth-web/web-settings.md)

**Shared entities:** [`User`](../../shared/entities/user.md), [`UserProfile`](../../shared/entities/user-profile.md), [`UserPreferences`](../../shared/entities/user-preferences.md)

---

## 2. Key Responsibilities

- Return profile details.
- Validate and replace the mailing address.
- Merge partial preference updates.

**Explicitly NOT in scope:**

- Login identity and credentials — owned by [`api-auth-sessions`](../../components/electric-wealth-api-spring/api-auth-sessions.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| HTTP operations (4) in `openapi.json`, tag `Profile` | Web callers `web-advisor`, `web-dashboard`, `web-profile`, `web-settings`; web `src/api/schema.d.ts` must be regenerated; Node API parity | `openapi-diff`: `OpenApiSpecTest`, `ContractParityTest`, then `npm run verify` in `electric-wealth-web` |
| Internal refactor (no interface change) | This component's controller / repository only | `./mvnw test -Dtest=ProfileApiTest` |
| Data-entity schema change | Schema owner(s) `db-identity-and-auth` and every API feature reading the same tables | `schema-diff` on `electric-wealth-db` + full `./mvnw test` |
| Dependency upgrade | Whole API (single Maven module) | `./mvnw verify` incl. `OpenApiSpecTest` |

**Upstream dependents to notify:** `web-advisor`, `web-dashboard`, `web-profile`, `web-settings`

**Downstream services to monitor post-change:** `db-identity-and-auth`, `api-request-pipeline`, `api-contract-and-data-access`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
