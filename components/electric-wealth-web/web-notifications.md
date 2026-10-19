---
component_id: web-notifications
repo: web
type: ui-feature
description: >-
  Use when the user views the notification centre at /notifications, marks notifications read, or sees the unread badge on the top-bar bell.
triggers:
  - "notifications page"
  - "notification bell"
  - "unread badge"
  - "mark all as read"
  - "notification polling"
paths:
  - "src/features/notifications/**"
  - "src/components/Layout.tsx"
depends_on:
  - "api-notifications"
  - "web-api-client"
  - "web-design-system"
  - "web-i18n-and-formatting"
consumed_by: []
data_entities:
  - "Notification"
last_verified_against_commit: 7489670e43b9b3498218e9a1559254ff6a581ea3
last_feature_id: ""
source_doc: docs/web-app-internal/13-feature-modules.md
verification_signal: route-inventory-diff
---

# Notifications Page and Bell

> **Repo:** `web` ([repo summary](../../repos/electric-wealth-web/README.md)) · **Type:** `ui-feature` · **Source doc:** [`docs/web-app-internal/13-feature-modules.md`](../../../electric-wealth-web/docs/web-app-internal/13-feature-modules.md)

## 1. Overview

`NotificationsPage` plus the `NotificationBell` in `Layout`. `useNotifications()` polls every 60 s while mounted (the bell is always mounted when signed in); mark-read mutations update the cache in place.

**Routes:** `/notifications`

**Depends on:** [`api-notifications`](../../components/electric-wealth-api-spring/api-notifications.md), [`web-api-client`](../../components/electric-wealth-web/web-api-client.md), [`web-design-system`](../../components/electric-wealth-web/web-design-system.md), [`web-i18n-and-formatting`](../../components/electric-wealth-web/web-i18n-and-formatting.md)

**Shared entities:** [`Notification`](../../shared/entities/notification.md)

---

## 2. Key Responsibilities

- List notifications by type.
- Mark one or all as read.
- Show the unread count (capped `9+`).

**Explicitly NOT in scope:**

- Notification creation — owned by [`api-notifications`](../../components/electric-wealth-api-spring/api-notifications.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Routes `/notifications` | Bookmarks, `returnTo` redirects, dashboard deep-link mapping, navigation in `web-app-shell` | `route-inventory-diff` against `src/App.tsx`, then `npm run verify` |
| Internal refactor (no interface change) | This feature folder only | `npm run typecheck` (no automated UI tests exist) |
| Data-entity schema change | API schema change in `api-notifications` surfaces as type errors here | `npm run gen:api` then `npm run verify` |
| Dependency upgrade | Whole SPA bundle | `npm run build` |

**Upstream dependents to notify:** None modelled.

**Downstream services to monitor post-change:** `api-notifications`, `web-api-client`, `web-design-system`, `web-i18n-and-formatting`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `7489670e43b9b3498218e9a1559254ff6a581ea3` (2026-10-19)

**Open follow-ups from extraction:**

- Notification items are not links — the API schema has no deep-link field (Part A § 13.9).
