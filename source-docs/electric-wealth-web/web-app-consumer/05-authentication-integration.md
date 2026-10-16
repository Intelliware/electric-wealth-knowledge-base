---
title: "Authentication Integration"
block: reverse-eng/web-app/consumer/authentication-integration
part: B
source_repo: electric-wealth-web
source_version: 0.1.0
source_commit: bbb4907dd0d866223742c529ea65a75ec8af576f
extracted: 2026-10-16
---

## 5. Authentication Integration

### 5.1 Supported Authentication Patterns

| Pattern | Supported |
|---|---|
| First-party sign-in: email + password, then a 6-digit MFA code, against the Electric Wealth API | ✅ Only pattern |
| Online enrolment for existing clients (`/register`, 7 steps) that ends signed in | ✅ |
| OIDC / OAuth 2.0 / SAML single sign-on | ❌ |
| Signed token / JWT handoff from another product | ❌ |
| Shared cookies with a host product | ❌ (the app uses no auth cookies) |

> Demo system: the API accepts the password `Demo123!` for every seeded persona and the fixed MFA code `123456`.

### 5.2 OIDC Setup (Recommended)

Not supported. There is no identity-provider configuration, redirect URI or callback route
(`project.toml`: `identity_provider = "custom"`).

### 5.3 Signed JWT Handoff

Not supported. The app does not read tokens from the URL, `postMessage` or cookies; it only stores tokens it obtained
from its own sign-in or enrolment.

### 5.4 Iframe-Specific Considerations

Not applicable (no iframe mode, see [01](01-embedding-options.md)).

### 5.5 Session Lifecycle

| Aspect | Behaviour |
|---|---|
| Tokens | Opaque bearer access token + refresh token from `POST /api/auth/token` (access token valid 900 s on the API) |
| MFA | Required before any protected call; the app's MFA screen calls `POST /api/auth/mfa/verify` |
| Storage | Browser `sessionStorage`, per tab: a page refresh keeps the session; closing the tab ends it; each tab signs in separately |
| Refresh | Every 10 minutes while signed in (`POST /api/auth/refresh`; the refresh token rotates) |
| Idle timeout | 15 minutes without mouse, keyboard, scroll or touch input → signed out with an "inactivity" notice |
| Server-side expiry or revocation | Any `401` on a protected call → signed out with a "session expired" notice |
| Return to page | Opening a protected URL while signed out returns the user to that URL after sign-in |
| After enrolment | The session starts directly (no separate MFA step) |

### 5.6 Logout

Users sign out from the avatar menu. The app revokes the refresh token on the API (`POST /api/auth/logout`), clears
its tokens and all cached data, and shows the sign-in page. If the revoke call fails the user is still signed out
locally. There is no logout URL, no front- or back-channel logout, and no way for another product to end a web
session.

### 5.7 Multi-Tenant Embedding

Not applicable. The app serves a single firm; there is no tenant identifier in URLs, tokens or configuration.

### 5.8 Anti-Patterns

- Do not try to sign users in by passing credentials or tokens in links.
- Do not expect one sign-in to cover several tabs or the mobile app; sessions are per tab.
- Do not ship the demo credentials hints shown on the sign-in screens to real users.

