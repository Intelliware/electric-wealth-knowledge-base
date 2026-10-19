---
component_id: db-notifications
repo: db
type: database-schema
description: >-
  Use when a change touches the notifications table — notification types, read flags, ordering or seeded notifications.
triggers:
  - "notifications table"
  - "read flag"
  - "notification type"
  - "welcome notification"
  - "unread notifications"
paths:
  - "migrations/002_notifications.sql"
  - "seed/102_notifications.sql"
depends_on:
  - "db-identity-and-auth"
consumed_by:
  - "api-notifications"
  - "api-registration"
data_entities:
  - "Notification"
last_verified_against_commit: e6262fcddca7f4c0488248ca8881483d9b920f0a
last_feature_id: ""
source_doc: docs/database-internal/05-tables-detailed-documentation.md
verification_signal: schema-diff
---

# Notifications Schema

> **Repo:** `db` ([repo summary](../../repos/electric-wealth-db/README.md)) · **Type:** `database-schema` · **Source doc:** [`docs/database-internal/05-tables-detailed-documentation.md`](../../../electric-wealth-db/docs/database-internal/05-tables-detailed-documentation.md)

## 1. Overview

Single table introduced by migration 002, composite key `(user_id, id)`, with a `type` enumeration (`INFO`, `SUCCESS`, `WARNING`, `ACTION_REQUIRED`) and a 0/1 `read` flag. Rows come from seed data and from the welcome notification created by `api-registration`.

**Tables:** `notifications`

**Depends on:** [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md)

**Consumed by:** [`api-notifications`](../../components/electric-wealth-api-spring/api-notifications.md), [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md)

**Shared entities:** [`Notification`](../../shared/entities/notification.md)

---

## 2. Key Responsibilities

- Store per-user notifications with type and read state.
- Support newest-first listing (`created_at DESC, id`).

**Explicitly NOT in scope:**

- Mark-read semantics — owned by [`api-notifications`](../../components/electric-wealth-api-spring/api-notifications.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Tables `notifications` | Direct SQL readers `api-notifications`, `api-registration`; Node API (`electric-wealth-api`) shares the same file | `schema-diff` (data dictionary vs `pragma_table_info`), then `./mvnw test` in `electric-wealth-api-spring` |
| Internal refactor (no interface change) | Seed data / indexes only | `npm run db:reset` then `npm run db:status` |
| Data-entity schema change | New migration required; every API repository touching these tables | Part A § 20 new-table checklist + API test suite |
| Dependency upgrade | Node `node:sqlite` / SQLite version affects all schema components | `npm run db:reset` + API test suite |

**Upstream dependents to notify:** `api-notifications`, `api-registration` Also the Node API (`electric-wealth-api`, not in this workspace), which shares the database file.

**Downstream services to monitor post-change:** `db-identity-and-auth`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `e6262fcddca7f4c0488248ca8881483d9b920f0a` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
