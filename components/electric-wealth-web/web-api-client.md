---
component_id: web-api-client
repo: web
type: shared-library
description: >-
  Use when a change touches how the web app calls the API — the openapi-fetch client, generated schema.d.ts types, header middleware, ApiError unwrapping, or the TanStack Query hooks and their cache keys.
triggers:
  - "api client"
  - "openapi-fetch"
  - "schema.d.ts"
  - "gen:api"
  - "check:api"
  - "query hooks"
  - "useQuery"
  - "cache invalidation"
  - "ApiError"
  - "Accept-Language header"
paths:
  - "src/api/**"
depends_on:
  - "api-contract-and-data-access"
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
source_doc: docs/web-app-internal/08-api-integration.md
verification_signal: exported-symbols-diff
---

# Typed API Client and Query Hooks

> **Repo:** `web` ([repo summary](../../repos/electric-wealth-web/README.md)) · **Type:** `shared-library` · **Source doc:** [`docs/web-app-internal/08-api-integration.md`](../../../electric-wealth-web/docs/web-app-internal/08-api-integration.md)

## 1. Overview

`client.ts` creates a same-origin `openapi-fetch` client typed by `schema.d.ts`, which is generated from `../electric-wealth-api-spring/openapi.json` (`npm run gen:api`; `npm run check:api` fails when stale). Middleware adds the bearer token (read from `tokenStore`, owned by `web-sign-in-and-mfa`) and `Accept-Language`, and broadcasts `ew:unauthorized` on a 401 outside `/api/auth/`. `unwrap()` turns the error envelope into `ApiError`.

`hooks.ts` wraps 35 calls in `useQuery` / `useMutation` (retry once for network/5xx, never for 4xx). Auth, registration and document download call `api` directly.

**Depends on:** [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md)

**Consumed by:** [`web-accounts`](../../components/electric-wealth-web/web-accounts.md), [`web-advisor`](../../components/electric-wealth-web/web-advisor.md), [`web-app-shell`](../../components/electric-wealth-web/web-app-shell.md), [`web-dashboard`](../../components/electric-wealth-web/web-dashboard.md), [`web-documents`](../../components/electric-wealth-web/web-documents.md), [`web-education`](../../components/electric-wealth-web/web-education.md), [`web-goals`](../../components/electric-wealth-web/web-goals.md), [`web-market`](../../components/electric-wealth-web/web-market.md), [`web-notifications`](../../components/electric-wealth-web/web-notifications.md), [`web-profile`](../../components/electric-wealth-web/web-profile.md), [`web-registration-wizard`](../../components/electric-wealth-web/web-registration-wizard.md), [`web-settings`](../../components/electric-wealth-web/web-settings.md), [`web-sign-in-and-mfa`](../../components/electric-wealth-web/web-sign-in-and-mfa.md)

---

## 2. Key Responsibilities

- Keep the typed contract in sync with the API.
- Attach auth and language headers.
- Normalise errors.
- Provide cached query and mutation hooks.

**Explicitly NOT in scope:**

- The contract itself — owned by [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Exported symbols / shared behaviour | Every dependant: `web-accounts`, `web-advisor`, `web-app-shell`, `web-dashboard`, `web-documents`, `web-education`, `web-goals`, `web-market`, `web-notifications`, `web-profile`, `web-registration-wizard`, `web-settings`, `web-sign-in-and-mfa` | `exported-symbols-diff`, then the repo's full gate |
| Internal refactor (no interface change) | This component only | Repo gate (`./mvnw test` or `npm run verify`) |
| Data-entity schema change | Indirect — via the features that use this foundation | Repo gate |
| Dependency upgrade | Whole repo | `./mvnw verify` or `npm run build` |

**Upstream dependents to notify:** `web-accounts`, `web-advisor`, `web-app-shell`, `web-dashboard`, `web-documents`, `web-education`, `web-goals`, `web-market`, `web-notifications`, `web-profile`, `web-registration-wizard`, `web-settings`, `web-sign-in-and-mfa`

**Downstream services to monitor post-change:** `api-contract-and-data-access`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `7489670e43b9b3498218e9a1559254ff6a581ea3` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
