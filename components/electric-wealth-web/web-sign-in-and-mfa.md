---
component_id: web-sign-in-and-mfa
repo: web
type: ui-feature
description: >-
  Use when the user signs in on /login with email and password and the MFA code, or a change touches the browser session: the signed-out / MFA / signed-in phases, token storage in sessionStorage, the 10-minute token refresh, the 15-minute idle sign-out, or signing out on a 401.
triggers:
  - "login page"
  - "sign in"
  - "MFA page"
  - "one-time code"
  - "AuthContext"
  - "useAuth"
  - "token store"
  - "token refresh"
  - "idle timeout"
  - "sign out"
  - "session expired notice"
paths:
  - "src/features/auth/AuthPages.tsx"
  - "src/auth/**"
depends_on:
  - "api-auth-sessions"
  - "web-api-client"
  - "web-design-system"
  - "web-i18n-and-formatting"
consumed_by:
  - "web-app-shell"
  - "web-registration-wizard"
data_entities:
  - "Session"
last_verified_against_commit: 7489670e43b9b3498218e9a1559254ff6a581ea3
last_feature_id: ""
source_doc: docs/web-app-internal/09-authentication-and-authorization.md
verification_signal: route-inventory-diff
---

# Sign-in, MFA and Browser Session

> **Repo:** `web` ([repo summary](../../repos/electric-wealth-web/README.md)) · **Type:** `ui-feature` · **Source doc:** [`docs/web-app-internal/09-authentication-and-authorization.md`](../../../electric-wealth-web/docs/web-app-internal/09-authentication-and-authorization.md)

## 1. Overview

`AuthContext` holds the auth `phase` (`unauthenticated` → `mfa` → `authenticated`) and calls `/api/auth/token`, `/mfa/verify`, `/refresh` (every 10 minutes) and `/logout` itself, not through the query hooks. It signs the user out after 15 idle minutes, and on the `ew:unauthorized` event the API client fires on a 401. `startSession(tokens)` is the entry point the registration wizard uses.

`tokenStore` keeps the tokens in `sessionStorage`: a page refresh keeps you signed in, closing the tab signs you out. The API client reads the access token from it to attach to every request.

`LoginPage` and `MfaPage` are the screens; both live at `/login` and which one renders depends on `phase`. Phase changes swap the route tables in `web-app-shell`; after MFA the user lands on the stored `returnTo` location.

**Routes:** `/login`

**Depends on:** [`api-auth-sessions`](../../components/electric-wealth-api-spring/api-auth-sessions.md), [`web-api-client`](../../components/electric-wealth-web/web-api-client.md), [`web-design-system`](../../components/electric-wealth-web/web-design-system.md), [`web-i18n-and-formatting`](../../components/electric-wealth-web/web-i18n-and-formatting.md)

**Consumed by:** [`web-app-shell`](../../components/electric-wealth-web/web-app-shell.md), [`web-registration-wizard`](../../components/electric-wealth-web/web-registration-wizard.md)

**Shared entities:** [`Session`](../../shared/entities/session.md)

---

## 2. Key Responsibilities

- Collect credentials and the MFA code.
- Hold the auth phase and sign-out reason.
- Store tokens in sessionStorage and refresh them.
- Sign out on idle, expiry or 401.
- Show sign-out notices and demo hints.

**Explicitly NOT in scope:**

- Server-side session validity — owned by [`api-auth-sessions`](../../components/electric-wealth-api-spring/api-auth-sessions.md)
- Enrolment wizard — owned by [`web-registration-wizard`](../../components/electric-wealth-web/web-registration-wizard.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Routes `/login` | Bookmarks, `returnTo` redirects, dashboard deep-link mapping, navigation in `web-app-shell` | `route-inventory-diff` against `src/App.tsx`, then `npm run verify` |
| Internal refactor (no interface change) | This feature folder only | `npm run typecheck` (no automated UI tests exist) |
| Data-entity schema change | API schema change in `api-auth-sessions` surfaces as type errors here | `npm run gen:api` then `npm run verify` |
| Dependency upgrade | Whole SPA bundle | `npm run build` |

**Upstream dependents to notify:** `web-app-shell`, `web-registration-wizard`

**Downstream services to monitor post-change:** `api-auth-sessions`, `web-api-client`, `web-design-system`, `web-i18n-and-formatting`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `7489670e43b9b3498218e9a1559254ff6a581ea3` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
