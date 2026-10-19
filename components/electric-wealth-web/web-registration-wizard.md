---
component_id: web-registration-wizard
repo: web
type: ui-feature
description: >-
  Use when a branch client without online access enrols through the seven-step /register wizard (login details, identity, security questions, two-step method, trusted contact, consent, onboarding tour).
triggers:
  - "register"
  - "enrolment wizard"
  - "create online access"
  - "verify identity form"
  - "security questions form"
  - "trusted contact form"
  - "consent step"
paths:
  - "src/features/auth/RegisterPage.tsx"
depends_on:
  - "api-registration"
  - "web-auth-session"
  - "web-api-client"
  - "web-design-system"
  - "web-i18n-and-formatting"
consumed_by: []
data_entities:
  - "Registration"
  - "SecurityQuestion"
  - "SecurityAnswer"
last_verified_against_commit: 7489670e43b9b3498218e9a1559254ff6a581ea3
last_feature_id: ""
source_doc: docs/web-app-internal/13-feature-modules.md
verification_signal: route-inventory-diff
---

# Registration Wizard

> **Repo:** `web` ([repo summary](../../repos/electric-wealth-web/README.md)) · **Type:** `ui-feature` · **Source doc:** [`docs/web-app-internal/13-feature-modules.md`](../../../electric-wealth-web/docs/web-app-internal/13-feature-modules.md)

## 1. Overview

`RegisterPage` keeps step state locally and calls `/api/registration/*` directly through `api`; the API holds the draft between steps via `registrationId`. Step 4 (two-step method) is kept in state only; step 6 sends consents as all `true` and receives tokens; step 7 navigates to `/` and calls `startSession(tokens)`.

`REGISTRATION_NOT_FOUND` (timed-out draft) resets the wizard to step 1; other registration codes map to specific messages.

**Routes:** `/register`

**Depends on:** [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md), [`web-auth-session`](../../components/electric-wealth-web/web-auth-session.md), [`web-api-client`](../../components/electric-wealth-web/web-api-client.md), [`web-design-system`](../../components/electric-wealth-web/web-design-system.md), [`web-i18n-and-formatting`](../../components/electric-wealth-web/web-i18n-and-formatting.md)

**Shared entities:** [`Registration`](../../shared/entities/registration.md), [`SecurityQuestion`](../../shared/entities/security-question.md), [`SecurityAnswer`](../../shared/entities/security-answer.md)

---

## 2. Key Responsibilities

- Render and validate each wizard step.
- Call the registration API in order.
- Start a signed-in session on completion.

**Explicitly NOT in scope:**

- Draft storage and step-order rules — owned by [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Routes `/register` | Bookmarks, `returnTo` redirects, dashboard deep-link mapping, navigation in `web-app-shell` | `route-inventory-diff` against `src/App.tsx`, then `npm run verify` |
| Internal refactor (no interface change) | This feature folder only | `npm run typecheck` (no automated UI tests exist) |
| Data-entity schema change | API schema change in `api-registration` surfaces as type errors here | `npm run gen:api` then `npm run verify` |
| Dependency upgrade | Whole SPA bundle | `npm run build` |

**Upstream dependents to notify:** None modelled.

**Downstream services to monitor post-change:** `api-registration`, `web-auth-session`, `web-api-client`, `web-design-system`, `web-i18n-and-formatting`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `7489670e43b9b3498218e9a1559254ff6a581ea3` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
