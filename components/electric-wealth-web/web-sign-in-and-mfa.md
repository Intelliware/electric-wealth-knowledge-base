---
component_id: web-sign-in-and-mfa
repo: web
type: ui-feature
description: >-
  Use when the user signs in on /login with email and password, enters the six-digit MFA code, or sees the idle / expired sign-out notice.
triggers:
  - "login page"
  - "sign in screen"
  - "MFA page"
  - "one-time code"
  - "session expired notice"
  - "demo hint"
  - "returnTo"
paths:
  - "src/features/auth/AuthPages.tsx"
depends_on:
  - "web-auth-session"
  - "api-auth-sessions"
  - "web-design-system"
  - "web-i18n-and-formatting"
consumed_by: []
data_entities:
  - "Session"
last_verified_against_commit: 7489670e43b9b3498218e9a1559254ff6a581ea3
last_feature_id: ""
source_doc: docs/web-app-internal/13-feature-modules.md
verification_signal: route-inventory-diff
---

# Sign-in and MFA Pages

> **Repo:** `web` ([repo summary](../../repos/electric-wealth-web/README.md)) · **Type:** `ui-feature` · **Source doc:** [`docs/web-app-internal/13-feature-modules.md`](../../../electric-wealth-web/docs/web-app-internal/13-feature-modules.md)

## 1. Overview

`LoginPage` and `MfaPage` share the `/login` URL; which one renders depends on `useAuth().phase`. `AuthCard` provides the centred panel with a local-only language toggle. After MFA succeeds the router sends the user to the stored `returnTo` location.

The pages call `AuthContext.login` / `verifyMfa` / `logout` from `web-auth-session`, which in turn call the API's `/api/auth/*` endpoints directly (not through the query hooks).

**Routes:** `/login`

**Depends on:** [`web-auth-session`](../../components/electric-wealth-web/web-auth-session.md), [`api-auth-sessions`](../../components/electric-wealth-api-spring/api-auth-sessions.md), [`web-design-system`](../../components/electric-wealth-web/web-design-system.md), [`web-i18n-and-formatting`](../../components/electric-wealth-web/web-i18n-and-formatting.md)

**Shared entities:** [`Session`](../../shared/entities/session.md)

---

## 2. Key Responsibilities

- Collect credentials and the MFA code.
- Show sign-out reasons (`idle`, `expired`) and demo hints.
- Link to `/register`.

**Explicitly NOT in scope:**

- Token storage, refresh and idle timeout — owned by [`web-auth-session`](../../components/electric-wealth-web/web-auth-session.md)
- Enrolment wizard — owned by [`web-registration-wizard`](../../components/electric-wealth-web/web-registration-wizard.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Routes `/login` | Bookmarks, `returnTo` redirects, dashboard deep-link mapping, navigation in `web-app-shell` | `route-inventory-diff` against `src/App.tsx`, then `npm run verify` |
| Internal refactor (no interface change) | This feature folder only | `npm run typecheck` (no automated UI tests exist) |
| Data-entity schema change | API schema change in `api-auth-sessions` surfaces as type errors here | `npm run gen:api` then `npm run verify` |
| Dependency upgrade | Whole SPA bundle | `npm run build` |

**Upstream dependents to notify:** None modelled.

**Downstream services to monitor post-change:** `web-auth-session`, `api-auth-sessions`, `web-design-system`, `web-i18n-and-formatting`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `7489670e43b9b3498218e9a1559254ff6a581ea3` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
