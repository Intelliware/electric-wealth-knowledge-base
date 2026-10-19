---
component_id: web-advisor
repo: web
type: ui-feature
description: >-
  Use when the user books, reschedules or cancels an advisor meeting, or reads and sends secure messages at /advisor or /advisor/messages.
triggers:
  - "advisor page"
  - "book meeting"
  - "reschedule meeting"
  - "cancel meeting"
  - "booking panel"
  - "secure messages page"
  - "slot taken"
paths:
  - "src/features/advisor/**"
depends_on:
  - "api-advisor"
  - "api-profile-and-preferences"
  - "web-api-client"
  - "web-design-system"
  - "web-i18n-and-formatting"
consumed_by: []
data_entities:
  - "Advisor"
  - "Appointment"
  - "AdvisorMessage"
  - "UserPreferences"
last_verified_against_commit: 7489670e43b9b3498218e9a1559254ff6a581ea3
last_feature_id: ""
source_doc: docs/web-app-internal/13-feature-modules.md
verification_signal: route-inventory-diff
---

# Advisor Pages

> **Repo:** `web` ([repo summary](../../repos/electric-wealth-web/README.md)) · **Type:** `ui-feature` · **Source doc:** [`docs/web-app-internal/13-feature-modules.md`](../../../electric-wealth-web/docs/web-app-internal/13-feature-modules.md)

## 1. Overview

`AdvisorPage` shows the advisor card, a `BookingPanel` (meeting type defaults to the saved preference) that doubles as the reschedule panel, and upcoming / past meetings. `AdvisorMessagesPage` renders the thread and composer (≤ 2000 chars). Bookings and updates invalidate every `['advisor', …]` query.

**Routes:** `/advisor`, `/advisor/messages`

**Depends on:** [`api-advisor`](../../components/electric-wealth-api-spring/api-advisor.md), [`api-profile-and-preferences`](../../components/electric-wealth-api-spring/api-profile-and-preferences.md), [`web-api-client`](../../components/electric-wealth-web/web-api-client.md), [`web-design-system`](../../components/electric-wealth-web/web-design-system.md), [`web-i18n-and-formatting`](../../components/electric-wealth-web/web-i18n-and-formatting.md)

**Shared entities:** [`Advisor`](../../shared/entities/advisor.md), [`Appointment`](../../shared/entities/appointment.md), [`AdvisorMessage`](../../shared/entities/advisor-message.md), [`UserPreferences`](../../shared/entities/user-preferences.md)

---

## 2. Key Responsibilities

- Book, reschedule and cancel meetings.
- Show availability.
- Read and send messages.

**Explicitly NOT in scope:**

- Slot rules and conflict detection — owned by [`api-advisor`](../../components/electric-wealth-api-spring/api-advisor.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Routes `/advisor`, `/advisor/messages` | Bookmarks, `returnTo` redirects, dashboard deep-link mapping, navigation in `web-app-shell` | `route-inventory-diff` against `src/App.tsx`, then `npm run verify` |
| Internal refactor (no interface change) | This feature folder only | `npm run typecheck` (no automated UI tests exist) |
| Data-entity schema change | API schema change in `api-advisor`, `api-profile-and-preferences` surfaces as type errors here | `npm run gen:api` then `npm run verify` |
| Dependency upgrade | Whole SPA bundle | `npm run build` |

**Upstream dependents to notify:** None modelled.

**Downstream services to monitor post-change:** `api-advisor`, `api-profile-and-preferences`, `web-api-client`, `web-design-system`, `web-i18n-and-formatting`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `7489670e43b9b3498218e9a1559254ff6a581ea3` (2026-10-19)

**Open follow-ups from extraction:**

- No polling for new advisor replies (Part A § 13.8).
