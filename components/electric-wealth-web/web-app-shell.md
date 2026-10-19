---
component_id: web-app-shell
repo: web
type: ui-feature
description: >-
  Use when a change touches the signed-in / signed-out route tables, the top navigation and user menu, the 404 page, return-to-origin redirects, or how the SPA boots and proxies /api in development.
triggers:
  - "routing"
  - "route table"
  - "App.tsx"
  - "layout"
  - "top navigation"
  - "user menu"
  - "not found page"
  - "returnTo redirect"
  - "vite proxy"
  - "boot"
paths:
  - "src/App.tsx"
  - "src/main.tsx"
  - "src/components/Layout.tsx"
  - "index.html"
  - "vite.config.ts"
  - "tsconfig*.json"
  - "package.json"
depends_on:
  - "web-sign-in-and-mfa"
  - "web-api-client"
  - "web-design-system"
  - "web-i18n-and-formatting"
consumed_by: []
data_entities:
  - "UserPreferences"
last_verified_against_commit: 7489670e43b9b3498218e9a1559254ff6a581ea3
last_feature_id: ""
source_doc: docs/web-app-internal/05-routing-and-navigation.md
verification_signal: route-inventory-diff
---

# App Shell, Routing and Layout

> **Repo:** `web` ([repo summary](../../repos/electric-wealth-web/README.md)) · **Type:** `ui-feature` · **Source doc:** [`docs/web-app-internal/05-routing-and-navigation.md`](../../../electric-wealth-web/docs/web-app-internal/05-routing-and-navigation.md)

## 1. Overview

`App` renders one of two declarative `react-router` v7 route tables based on `useAuth().phase`; there are no per-route guards — while signed out the protected routes do not exist and every URL redirects to `/login` with `returnTo` in router state. Signed-in pages render inside the pathless `Layout` route (top nav, bell, user menu, language toggle that saves `{ language }`).

`main.tsx` wires `BrowserRouter`, the TanStack Query client and the auth provider. In development Vite proxies `/api` to the Spring API (`API_URL`).

**Routes:** `/*`

**Depends on:** [`web-sign-in-and-mfa`](../../components/electric-wealth-web/web-sign-in-and-mfa.md), [`web-api-client`](../../components/electric-wealth-web/web-api-client.md), [`web-design-system`](../../components/electric-wealth-web/web-design-system.md), [`web-i18n-and-formatting`](../../components/electric-wealth-web/web-i18n-and-formatting.md)

**Shared entities:** [`UserPreferences`](../../shared/entities/user-preferences.md)

---

## 2. Key Responsibilities

- Own the route tables and redirects.
- Render the layout and navigation.
- Boot providers.
- Configure the dev server and build.

**Explicitly NOT in scope:**

- Individual pages — owned by [`web-dashboard`](../../components/electric-wealth-web/web-dashboard.md)
- Auth phase transitions — owned by [`web-sign-in-and-mfa`](../../components/electric-wealth-web/web-sign-in-and-mfa.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Routes `/*` | Bookmarks, `returnTo` redirects, dashboard deep-link mapping, navigation in `web-app-shell` | `route-inventory-diff` against `src/App.tsx`, then `npm run verify` |
| Internal refactor (no interface change) | This feature folder only | `npm run typecheck` (no automated UI tests exist) |
| Data-entity schema change | API schema change in the API surfaces as type errors here | `npm run gen:api` then `npm run verify` |
| Dependency upgrade | Whole SPA bundle | `npm run build` |

**Upstream dependents to notify:** None modelled.

**Downstream services to monitor post-change:** `web-sign-in-and-mfa`, `web-api-client`, `web-design-system`, `web-i18n-and-formatting`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `7489670e43b9b3498218e9a1559254ff6a581ea3` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
