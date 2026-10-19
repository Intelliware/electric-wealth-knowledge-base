---
component_id: web-auth-session
repo: web
type: shared-library
description: >-
  Use when a change touches the browser-side session — AuthContext phases, token storage, the 10-minute refresh timer, idle sign-out, or the global 401 handling that signs the user out.
triggers:
  - "AuthContext"
  - "useAuth"
  - "token store"
  - "refresh timer"
  - "idle timeout"
  - "sign out"
  - "ew:unauthorized"
  - "auth phase"
paths:
  - "src/auth/**"
depends_on:
  - "web-api-client"
  - "api-auth-sessions"
consumed_by:
  - "web-app-shell"
  - "web-registration-wizard"
  - "web-sign-in-and-mfa"
data_entities:
  - "Session"
last_verified_against_commit: 7489670e43b9b3498218e9a1559254ff6a581ea3
last_feature_id: ""
source_doc: docs/web-app-internal/09-authentication-and-authorization.md
verification_signal: exported-symbols-diff
---

# Auth Session Context

> **Repo:** `web` ([repo summary](../../repos/electric-wealth-web/README.md)) · **Type:** `shared-library` · **Source doc:** [`docs/web-app-internal/09-authentication-and-authorization.md`](../../../electric-wealth-web/docs/web-app-internal/09-authentication-and-authorization.md)

## 1. Overview

`AuthContext` owns the `phase` state machine (`unauthenticated` → `mfa` → `authenticated`) and calls `/api/auth/token`, `/mfa/verify`, `/refresh` (every 10 minutes) and `/logout` directly. `tokenStore` holds the tokens used by the client middleware. A `window` event `ew:unauthorized` from the client triggers `logout('expired')`.

Phase changes swap the route tables in `web-app-shell`; `startSession(tokens)` is the entry point used by the registration wizard.

**Depends on:** [`web-api-client`](../../components/electric-wealth-web/web-api-client.md), [`api-auth-sessions`](../../components/electric-wealth-api-spring/api-auth-sessions.md)

**Consumed by:** [`web-app-shell`](../../components/electric-wealth-web/web-app-shell.md), [`web-registration-wizard`](../../components/electric-wealth-web/web-registration-wizard.md), [`web-sign-in-and-mfa`](../../components/electric-wealth-web/web-sign-in-and-mfa.md)

**Shared entities:** [`Session`](../../shared/entities/session.md)

---

## 2. Key Responsibilities

- Hold and expose auth phase and sign-out reason.
- Store and refresh tokens.
- Sign out on idle, expiry or 401.

**Explicitly NOT in scope:**

- Server-side session validity — owned by [`api-auth-sessions`](../../components/electric-wealth-api-spring/api-auth-sessions.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Exported symbols / shared behaviour | Every dependant: `web-app-shell`, `web-registration-wizard`, `web-sign-in-and-mfa` | `exported-symbols-diff`, then the repo's full gate |
| Internal refactor (no interface change) | This component only | Repo gate (`./mvnw test` or `npm run verify`) |
| Data-entity schema change | Indirect — via the features that use this foundation | Repo gate |
| Dependency upgrade | Whole repo | `./mvnw verify` or `npm run build` |

**Upstream dependents to notify:** `web-app-shell`, `web-registration-wizard`, `web-sign-in-and-mfa`

**Downstream services to monitor post-change:** `web-api-client`, `api-auth-sessions`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `7489670e43b9b3498218e9a1559254ff6a581ea3` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
