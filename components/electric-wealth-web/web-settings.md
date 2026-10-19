---
component_id: web-settings
repo: web
type: ui-feature
description: >-
  Use when the user changes theme, language, notification channels, document delivery or default meeting type on /settings.
triggers:
  - "settings page"
  - "theme switch"
  - "dark mode"
  - "language setting"
  - "notification channels"
  - "document delivery"
  - "default meeting type"
paths:
  - "src/features/settings/**"
depends_on:
  - "api-profile-and-preferences"
  - "web-api-client"
  - "web-design-system"
  - "web-i18n-and-formatting"
consumed_by: []
data_entities:
  - "UserPreferences"
last_verified_against_commit: 7489670e43b9b3498218e9a1559254ff6a581ea3
last_feature_id: ""
source_doc: docs/web-app-internal/13-feature-modules.md
verification_signal: route-inventory-diff
---

# Settings Page

> **Repo:** `web` ([repo summary](../../repos/electric-wealth-web/README.md)) · **Type:** `ui-feature` · **Source doc:** [`docs/web-app-internal/13-feature-modules.md`](../../../electric-wealth-web/docs/web-app-internal/13-feature-modules.md)

## 1. Overview

Each control saves immediately through an optimistic `useUpdatePreferences()`; changing language also calls `setAppLocale`. An `aria-live` line announces save status.

**Routes:** `/settings`

**Depends on:** [`api-profile-and-preferences`](../../components/electric-wealth-api-spring/api-profile-and-preferences.md), [`web-api-client`](../../components/electric-wealth-web/web-api-client.md), [`web-design-system`](../../components/electric-wealth-web/web-design-system.md), [`web-i18n-and-formatting`](../../components/electric-wealth-web/web-i18n-and-formatting.md)

**Shared entities:** [`UserPreferences`](../../shared/entities/user-preferences.md)

---

## 2. Key Responsibilities

- Edit and persist preferences.
- Apply theme and language changes immediately.

**Explicitly NOT in scope:**

- Preference merge rules — owned by [`api-profile-and-preferences`](../../components/electric-wealth-api-spring/api-profile-and-preferences.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Routes `/settings` | Bookmarks, `returnTo` redirects, dashboard deep-link mapping, navigation in `web-app-shell` | `route-inventory-diff` against `src/App.tsx`, then `npm run verify` |
| Internal refactor (no interface change) | This feature folder only | `npm run typecheck` (no automated UI tests exist) |
| Data-entity schema change | API schema change in `api-profile-and-preferences` surfaces as type errors here | `npm run gen:api` then `npm run verify` |
| Dependency upgrade | Whole SPA bundle | `npm run build` |

**Upstream dependents to notify:** None modelled.

**Downstream services to monitor post-change:** `api-profile-and-preferences`, `web-api-client`, `web-design-system`, `web-i18n-and-formatting`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `7489670e43b9b3498218e9a1559254ff6a581ea3` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
