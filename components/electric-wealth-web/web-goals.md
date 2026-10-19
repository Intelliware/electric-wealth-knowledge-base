---
component_id: web-goals
repo: web
type: ui-feature
description: >-
  Use when the user views savings goals or adds a goal on /goals.
triggers:
  - "goals page"
  - "add goal form"
  - "goal progress bar"
  - "on track"
  - "goal card"
paths:
  - "src/features/goals/**"
depends_on:
  - "api-goals"
  - "web-api-client"
  - "web-design-system"
  - "web-i18n-and-formatting"
consumed_by: []
data_entities:
  - "Goal"
last_verified_against_commit: 7489670e43b9b3498218e9a1559254ff6a581ea3
last_feature_id: ""
source_doc: docs/web-app-internal/13-feature-modules.md
verification_signal: route-inventory-diff
---

# Goals Page

> **Repo:** `web` ([repo summary](../../repos/electric-wealth-web/README.md)) · **Type:** `ui-feature` · **Source doc:** [`docs/web-app-internal/13-feature-modules.md`](../../../electric-wealth-web/docs/web-app-internal/13-feature-modules.md)

## 1. Overview

Goal cards with progress and an inline `GoalForm`. Amounts are converted from dollars to cents before posting; currency is always `CAD`. "On track" is a UI rule: `progressPct >= 50`.

**Routes:** `/goals`

**Depends on:** [`api-goals`](../../components/electric-wealth-api-spring/api-goals.md), [`web-api-client`](../../components/electric-wealth-web/web-api-client.md), [`web-design-system`](../../components/electric-wealth-web/web-design-system.md), [`web-i18n-and-formatting`](../../components/electric-wealth-web/web-i18n-and-formatting.md)

**Shared entities:** [`Goal`](../../shared/entities/goal.md)

---

## 2. Key Responsibilities

- List goals with progress.
- Create a goal.

**Explicitly NOT in scope:**

- Goal validation — owned by [`api-goals`](../../components/electric-wealth-api-spring/api-goals.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Routes `/goals` | Bookmarks, `returnTo` redirects, dashboard deep-link mapping, navigation in `web-app-shell` | `route-inventory-diff` against `src/App.tsx`, then `npm run verify` |
| Internal refactor (no interface change) | This feature folder only | `npm run typecheck` (no automated UI tests exist) |
| Data-entity schema change | API schema change in `api-goals` surfaces as type errors here | `npm run gen:api` then `npm run verify` |
| Dependency upgrade | Whole SPA bundle | `npm run build` |

**Upstream dependents to notify:** None modelled.

**Downstream services to monitor post-change:** `api-goals`, `web-api-client`, `web-design-system`, `web-i18n-and-formatting`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `7489670e43b9b3498218e9a1559254ff6a581ea3` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
