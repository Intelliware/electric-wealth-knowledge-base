# Electric Wealth Web App — Repository Summary

> **Repo code:** `web`
> **Role:** ui
> **Type:** react-19-vite-typescript
> **Path in source tree:** `{project-root}/../electric-wealth-web`
> **Primary:** no
> **Last indexed commit:** 7489670e43b9b3498218e9a1559254ff6a581ea3

This page is the per-repository entry point inside the Electric Wealth knowledge base. The
[`ARCHITECTURE.md`](../../ARCHITECTURE.md) Repository Map (§ 2) links here. KB folder slug: `electric-wealth-web`.

---

## 1. Purpose

The browser portal for Electric Wealth retail investors: a client-side React SPA with 18 pages in 11 feature folders, bilingual (en-CA / fr-CA). Every piece of data comes from the Spring API through typed calls generated from its OpenAPI contract; there is no backend of its own and no SSR. Without it, investors have no web front end (a mobile app exists outside this workspace).

---

## 2. Owner

| Field | Value |
|---|---|
| Owning team | Not recorded in RE output |
| Primary maintainer | Not recorded in RE output |
| Backup maintainer | Not recorded in RE output |
| Contact channel | Not recorded in RE output |
| On-call rotation | None defined (local / workshop project) |

> Ownership is captured by `capture-solution-context` (`shared/solution-context/50-ownership-and-raci.md`), which has not run yet.

---

## 3. Tech Stack

| Layer | Technology / Version | Notes |
|---|---|---|
| Language(s) | TypeScript ~5.9 (`strict`) |  |
| Framework(s) | React 19, react-router 7, TanStack Query 5, i18next | Declarative routes, no data routers |
| Build / package manager | Vite 8, npm, Node.js ≥ 24 |  |
| Test stack | None | Static gate only: `npm run verify` (`check:api` + `typecheck`) |
| Datastore(s) | Browser `sessionStorage` (auth tokens only, via `tokenStore`) |  |
| Runtime / deploy target | Vite dev server on 5173 proxying `/api` to `API_URL` (default `http://localhost:4200`) | No deployment target defined |
| Key dependencies (top 5–10) | react, react-router, @tanstack/react-query, openapi-fetch, openapi-typescript, i18next, react-i18next | Hand-written CSS and SVG charts |

---

## 4. Top Components

Full list: Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).

| Component ID | Type | Description (Use-when …) | Doc |
|---|---|---|---|
| [`web-api-client`](../../components/electric-wealth-web/web-api-client.md) | `shared-library` | Use when a change touches how the web app calls the API — the openapi-fetch client, generated schema.d.ts types, header middleware, ApiError unwrapping, or the TanStack Query hooks and their cache keys. | [doc](../../components/electric-wealth-web/web-api-client.md) |
| [`web-sign-in-and-mfa`](../../components/electric-wealth-web/web-sign-in-and-mfa.md) | `ui-feature` | Use when the user signs in on /login with email and password and the MFA code, or a change touches the browser session: the signed-out / MFA / signed-in phases, token storage in sessionStorage, the 10-minute token refresh, the 15-minute idle sign-out, or signing out on a 401. | [doc](../../components/electric-wealth-web/web-sign-in-and-mfa.md) |
| [`web-app-shell`](../../components/electric-wealth-web/web-app-shell.md) | `ui-feature` | Use when a change touches the signed-in / signed-out route tables, the top navigation and user menu, the 404 page, return-to-origin redirects, or how the SPA boots and proxies /api in development. | [doc](../../components/electric-wealth-web/web-app-shell.md) |
| [`web-dashboard`](../../components/electric-wealth-web/web-dashboard.md) | `ui-feature` | Use when the signed-in home page (/) shows the portfolio total, day change, action-required alerts, allocation bar, goal progress and quick links — including mapping API deep links to web routes. | [doc](../../components/electric-wealth-web/web-dashboard.md) |
| [`web-accounts`](../../components/electric-wealth-web/web-accounts.md) | `ui-feature` | Use when the user views the accounts list, an account's detail (performance vs benchmark, holdings, transactions, nickname) or opens a new account at /accounts, /accounts/:id or /accounts/new. | [doc](../../components/electric-wealth-web/web-accounts.md) |
| [`web-registration-wizard`](../../components/electric-wealth-web/web-registration-wizard.md) | `ui-feature` | Use when a branch client without online access enrols through the seven-step /register wizard (login details, identity, security questions, two-step method, trusted contact, consent, onboarding tour). | [doc](../../components/electric-wealth-web/web-registration-wizard.md) |
| [`web-advisor`](../../components/electric-wealth-web/web-advisor.md) | `ui-feature` | Use when the user books, reschedules or cancels an advisor meeting, or reads and sends secure messages at /advisor or /advisor/messages. | [doc](../../components/electric-wealth-web/web-advisor.md) |
| [`web-market`](../../components/electric-wealth-web/web-market.md) | `ui-feature` | Use when the user searches securities, manages the watchlist, reads news and the weekly roundup, views a security's price chart, or browses thematic baskets at /market, /market/:symbol or /market/baskets. | [doc](../../components/electric-wealth-web/web-market.md) |

---

## 5. Public Interfaces

| Kind | Identifier | Description | Source of Truth |
|---|---|---|---|
| `http-endpoint` | SPA route `/login` | Sign-in, MFA and Browser Session (`web-sign-in-and-mfa`) | `src/App.tsx` |
| `http-endpoint` | SPA route `/register` | Registration Wizard (`web-registration-wizard`) | `src/App.tsx` |
| `http-endpoint` | SPA route `/` | Dashboard Page (`web-dashboard`) | `src/App.tsx` |
| `http-endpoint` | SPA route `/accounts` | Accounts Pages (`web-accounts`) | `src/App.tsx` |
| `http-endpoint` | SPA route `/accounts/new` | Accounts Pages (`web-accounts`) | `src/App.tsx` |
| `http-endpoint` | SPA route `/accounts/:id` | Accounts Pages (`web-accounts`) | `src/App.tsx` |
| `http-endpoint` | SPA route `/documents` | Documents Page (`web-documents`) | `src/App.tsx` |
| `http-endpoint` | SPA route `/goals` | Goals Page (`web-goals`) | `src/App.tsx` |
| `http-endpoint` | SPA route `/market` | Market Pages (`web-market`) | `src/App.tsx` |
| `http-endpoint` | SPA route `/market/:symbol` | Market Pages (`web-market`) | `src/App.tsx` |
| `http-endpoint` | SPA route `/market/baskets` | Market Pages (`web-market`) | `src/App.tsx` |
| `http-endpoint` | SPA route `/learn` | Learning Centre Pages (`web-education`) | `src/App.tsx` |
| `http-endpoint` | SPA route `/learn/glossary` | Learning Centre Pages (`web-education`) | `src/App.tsx` |
| `http-endpoint` | SPA route `/advisor` | Advisor Pages (`web-advisor`) | `src/App.tsx` |
| `http-endpoint` | SPA route `/advisor/messages` | Advisor Pages (`web-advisor`) | `src/App.tsx` |
| `http-endpoint` | SPA route `/notifications` | Notifications Page and Bell (`web-notifications`) | `src/App.tsx` |
| `http-endpoint` | SPA route `/profile` | Profile Page (`web-profile`) | `src/App.tsx` |
| `http-endpoint` | SPA route `/settings` | Settings Page (`web-settings`) | `src/App.tsx` |
| `http-endpoint` | SPA route `/*` | App Shell, Routing and Layout (`web-app-shell`) | `src/App.tsx` |
| `config-knob` | `API_URL` | Dev-proxy target for `/api` | `vite.config.ts` |

---

## 6. Internal & Consumer Doc Pointers

Part A paths are relative to the source repo's root; Part B lives in this KB under `source-docs/` (KB-relative path shown).

| Document | Audience | Location |
|---|---|---|
| Part A — Internal docs | Maintainers of web | [`docs/web-app-internal/`](../../../electric-wealth-web/docs/web-app-internal/01-application-overview.md) (source repo) |
| Part B — Consumer / Implementer docs | Downstream callers | [`source-docs/electric-wealth-web/web-app-consumer/`](../../source-docs/electric-wealth-web/web-app-consumer/) (KB) |
| API contract (OpenAPI / AsyncAPI / GraphQL SDL) | Tooling, integration tests | Consumes the API contract; generated types in [`src/api/schema.d.ts`](../../../electric-wealth-web/src/api/schema.d.ts) |
| Architecture Decision Records (repo-local) | Maintainers, architects | |
| Runbooks / on-call playbooks | On-call engineers | |
| Onboarding guide | New contributors | [`README.md`](../../../electric-wealth-web/README.md) |
| RE verification report | KB maintainers | [`docs/web-app-internal/verification-report.md`](../../../electric-wealth-web/docs/web-app-internal/verification-report.md) — `MECHANICAL: GO` |
