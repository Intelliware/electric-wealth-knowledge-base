---
component_id: web-design-system
repo: web
type: framework
description: >-
  Use when a change touches shared UI primitives (cards, buttons, empty/error states, progress bars, pagination), the SVG charts, CSS theme variables, or dark-mode styling.
triggers:
  - "ui components"
  - "design system"
  - "charts"
  - "styles.css"
  - "theme variables"
  - "dark mode"
  - "ErrorState"
  - "EmptyState"
  - "accessibility"
paths:
  - "src/components/ui.tsx"
  - "src/components/charts.tsx"
  - "src/styles.css"
  - "public/**"
depends_on: []
consumed_by:
  - "web-accounts"
  - "web-advisor"
  - "web-app-shell"
  - "web-dashboard"
  - "web-documents"
  - "web-education"
  - "web-goals"
  - "web-market"
  - "web-notifications"
  - "web-profile"
  - "web-registration-wizard"
  - "web-settings"
  - "web-sign-in-and-mfa"
data_entities: []
last_verified_against_commit: 7489670e43b9b3498218e9a1559254ff6a581ea3
last_feature_id: ""
source_doc: docs/web-app-internal/11-styling-and-design-system.md
verification_signal: exported-symbols-diff
---

# UI Components, Charts and Styles

> **Repo:** `web` ([repo summary](../../repos/electric-wealth-web/README.md)) · **Type:** `framework` · **Source doc:** [`docs/web-app-internal/11-styling-and-design-system.md`](../../../electric-wealth-web/docs/web-app-internal/11-styling-and-design-system.md)

## 1. Overview

Hand-written components in `ui.tsx` (`Card`, `PageHeader`, `ProgressBar`, `ErrorState`, `EmptyState`, `Pagination`, …) and `charts.tsx` (`PerformanceChart`, `PriceChart`, `AllocationBar`), styled by a single `styles.css` with CSS custom properties for light/dark themes. No component library is used.

**Consumed by:** [`web-accounts`](../../components/electric-wealth-web/web-accounts.md), [`web-advisor`](../../components/electric-wealth-web/web-advisor.md), [`web-app-shell`](../../components/electric-wealth-web/web-app-shell.md), [`web-dashboard`](../../components/electric-wealth-web/web-dashboard.md), [`web-documents`](../../components/electric-wealth-web/web-documents.md), [`web-education`](../../components/electric-wealth-web/web-education.md), [`web-goals`](../../components/electric-wealth-web/web-goals.md), [`web-market`](../../components/electric-wealth-web/web-market.md), [`web-notifications`](../../components/electric-wealth-web/web-notifications.md), [`web-profile`](../../components/electric-wealth-web/web-profile.md), [`web-registration-wizard`](../../components/electric-wealth-web/web-registration-wizard.md), [`web-settings`](../../components/electric-wealth-web/web-settings.md), [`web-sign-in-and-mfa`](../../components/electric-wealth-web/web-sign-in-and-mfa.md)

---

## 2. Key Responsibilities

- Provide reusable, accessible UI primitives.
- Render charts.
- Define theme tokens and global styles.

**Explicitly NOT in scope:**

- Text and number formatting — owned by [`web-i18n-and-formatting`](../../components/electric-wealth-web/web-i18n-and-formatting.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Exported symbols / shared behaviour | Every dependant: `web-accounts`, `web-advisor`, `web-app-shell`, `web-dashboard`, `web-documents`, `web-education`, `web-goals`, `web-market`, `web-notifications`, `web-profile`, `web-registration-wizard`, `web-settings`, `web-sign-in-and-mfa` | `exported-symbols-diff`, then the repo's full gate |
| Internal refactor (no interface change) | This component only | Repo gate (`./mvnw test` or `npm run verify`) |
| Data-entity schema change | Indirect — via the features that use this foundation | Repo gate |
| Dependency upgrade | Whole repo | `./mvnw verify` or `npm run build` |

**Upstream dependents to notify:** `web-accounts`, `web-advisor`, `web-app-shell`, `web-dashboard`, `web-documents`, `web-education`, `web-goals`, `web-market`, `web-notifications`, `web-profile`, `web-registration-wizard`, `web-settings`, `web-sign-in-and-mfa`

**Downstream services to monitor post-change:** None.

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `7489670e43b9b3498218e9a1559254ff6a581ea3` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
