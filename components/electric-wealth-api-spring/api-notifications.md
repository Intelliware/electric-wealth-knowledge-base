---
component_id: api-notifications
repo: api-spring
type: api-feature
description: >-
  Use when a client lists notifications or marks one or all of them as read through /api/notifications.
triggers:
  - "notifications"
  - "mark as read"
  - "mark all read"
  - "unread count"
  - "notification centre"
paths:
  - "src/main/java/com/electricwealth/api/web/NotificationController.java"
  - "src/main/java/com/electricwealth/api/dto/NotificationDtos.java"
  - "src/main/java/com/electricwealth/api/repository/NotificationRepository.java"
  - "src/test/java/com/electricwealth/api/ProfileApiTest.java"
depends_on:
  - "db-notifications"
  - "api-request-pipeline"
  - "api-contract-and-data-access"
consumed_by:
  - "web-notifications"
data_entities:
  - "Notification"
last_verified_against_commit: 3f7403e33d97b39872e89690431c7880dea98ae4
last_feature_id: ""
source_doc: docs/api-internal/12-domain-models-and-business-rules.md
verification_signal: openapi-diff
---

# Notifications

> **Repo:** `api-spring` ([repo summary](../../repos/electric-wealth-api-spring/README.md)) · **Type:** `api-feature` · **Source doc:** [`docs/api-internal/12-domain-models-and-business-rules.md`](../../../electric-wealth-api-spring/docs/api-internal/12-domain-models-and-business-rules.md)

## 1. Overview

Returns all notifications newest first (`created_at DESC, id`); `read-all` returns how many were unread. Notifications carry no deep link.

**Operations:** `GET /api/notifications`, `POST /api/notifications/{id}/read`, `POST /api/notifications/read-all`

**Depends on:** [`db-notifications`](../../components/electric-wealth-db/db-notifications.md), [`api-request-pipeline`](../../components/electric-wealth-api-spring/api-request-pipeline.md), [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md)

**Consumed by:** [`web-notifications`](../../components/electric-wealth-web/web-notifications.md)

**Shared entities:** [`Notification`](../../shared/entities/notification.md)

---

## 2. Key Responsibilities

- List notifications.
- Mark one or all as read.

**Explicitly NOT in scope:**

- Creating notifications (seed / registration welcome) — owned by [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| HTTP operations (3) in `openapi.json`, tag `Notifications` | Web callers `web-notifications`; web `src/api/schema.d.ts` must be regenerated; Node API parity | `openapi-diff`: `OpenApiSpecTest`, `ContractParityTest`, then `npm run verify` in `electric-wealth-web` |
| Internal refactor (no interface change) | This component's controller / repository only | `./mvnw test -Dtest=ProfileApiTest` |
| Data-entity schema change | Schema owner(s) `db-notifications` and every API feature reading the same tables | `schema-diff` on `electric-wealth-db` + full `./mvnw test` |
| Dependency upgrade | Whole API (single Maven module) | `./mvnw verify` incl. `OpenApiSpecTest` |

**Upstream dependents to notify:** `web-notifications`

**Downstream services to monitor post-change:** `db-notifications`, `api-request-pipeline`, `api-contract-and-data-access`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
