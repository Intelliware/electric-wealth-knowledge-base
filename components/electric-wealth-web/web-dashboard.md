---
component_id: web-dashboard
repo: web
type: ui-feature
description: >-
  Use when the signed-in home page (/) shows the portfolio total, day change, action-required alerts, allocation bar, goal progress and quick links — including mapping API deep links to web routes.
triggers:
  - "dashboard page"
  - "home page"
  - "portfolio total"
  - "allocation bar"
  - "alerts"
  - "quick links"
  - "deep links"
  - "Mobile app only"
paths:
  - "src/features/dashboard/DashboardPage.tsx"
  - "src/lib/deepLinks.ts"
depends_on:
  - "api-dashboard"
  - "api-profile-and-preferences"
  - "web-api-client"
  - "web-design-system"
  - "web-i18n-and-formatting"
consumed_by: []
data_entities:
  - "PortfolioSnapshot"
  - "AssetAllocation"
  - "Alert"
  - "QuickLink"
  - "Goal"
  - "UserProfile"
last_verified_against_commit: 7489670e43b9b3498218e9a1559254ff6a581ea3
last_feature_id: ""
source_doc: docs/web-app-internal/13-feature-modules.md
verification_signal: route-inventory-diff
---

# Dashboard Page

> **Repo:** `web` ([repo summary](../../repos/electric-wealth-web/README.md)) · **Type:** `ui-feature` · **Source doc:** [`docs/web-app-internal/13-feature-modules.md`](../../../electric-wealth-web/docs/web-app-internal/13-feature-modules.md)

## 1. Overview

Renders `useDashboard()` (keyed by locale) plus `useProfile()` for the greeting. Alerts and quick links carry mobile deep links (`electricwealth://…`); `deepLinkToRoute()` maps them to web routes and anything unmapped is rendered without a link and tagged "Mobile app only".

**Routes:** `/`

**Depends on:** [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md), [`api-profile-and-preferences`](../../components/electric-wealth-api-spring/api-profile-and-preferences.md), [`web-api-client`](../../components/electric-wealth-web/web-api-client.md), [`web-design-system`](../../components/electric-wealth-web/web-design-system.md), [`web-i18n-and-formatting`](../../components/electric-wealth-web/web-i18n-and-formatting.md)

**Shared entities:** [`PortfolioSnapshot`](../../shared/entities/portfolio-snapshot.md), [`AssetAllocation`](../../shared/entities/asset-allocation.md), [`Alert`](../../shared/entities/alert.md), [`QuickLink`](../../shared/entities/quick-link.md), [`Goal`](../../shared/entities/goal.md), [`UserProfile`](../../shared/entities/user-profile.md)

---

## 2. Key Responsibilities

- Render the portfolio hero, alerts, allocation, goals and quick links.
- Translate mobile deep links into web routes.

**Explicitly NOT in scope:**

- Dashboard data assembly — owned by [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md)
- Goal creation — owned by [`web-goals`](../../components/electric-wealth-web/web-goals.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Routes `/` | Bookmarks, `returnTo` redirects, dashboard deep-link mapping, navigation in `web-app-shell` | `route-inventory-diff` against `src/App.tsx`, then `npm run verify` |
| Internal refactor (no interface change) | This feature folder only | `npm run typecheck` (no automated UI tests exist) |
| Data-entity schema change | API schema change in `api-dashboard`, `api-profile-and-preferences` surfaces as type errors here | `npm run gen:api` then `npm run verify` |
| Dependency upgrade | Whole SPA bundle | `npm run build` |

**Upstream dependents to notify:** None modelled.

**Downstream services to monitor post-change:** `api-dashboard`, `api-profile-and-preferences`, `web-api-client`, `web-design-system`, `web-i18n-and-formatting`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `7489670e43b9b3498218e9a1559254ff6a581ea3` (2026-10-19)

**Open follow-ups from extraction:**

- Greeting always says "Good morning" regardless of time of day (Part A § 13.2).
