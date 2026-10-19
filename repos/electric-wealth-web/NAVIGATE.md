# NAVIGATE — `web` (electric-wealth-web)

**Role and primary tech:** Browser UI — React 19 + Vite 8 + TypeScript SPA, typed from the API's OpenAPI contract.

When the user's task touches this repo, read [`README.md`](README.md) first (ownership, tech stack, public
interfaces), then walk [`components/electric-wealth-web/`](../../components/electric-wealth-web/) and follow `consumed_by` /
`depends_on` outwards (or query [`graph.json`](../../graph.json)). Do not read every component — pick by
`triggers` and `description`, default cap 7.

Source repo: `../electric-wealth-web` at commit `7489670e43b9b3498218e9a1559254ff6a581ea3`. Part A (internal) docs live in the source repo under
`docs/web-app-internal/`; Part B (consumer) docs live in this KB under `source-docs/electric-wealth-web/web-app-consumer/`.

## Components in this repo

- [`web-sign-in-and-mfa`](../../components/electric-wealth-web/web-sign-in-and-mfa.md) — Sign-in, MFA and Browser Session
- [`web-registration-wizard`](../../components/electric-wealth-web/web-registration-wizard.md) — Registration Wizard
- [`web-dashboard`](../../components/electric-wealth-web/web-dashboard.md) — Dashboard Page
- [`web-accounts`](../../components/electric-wealth-web/web-accounts.md) — Accounts Pages
- [`web-documents`](../../components/electric-wealth-web/web-documents.md) — Documents Page
- [`web-goals`](../../components/electric-wealth-web/web-goals.md) — Goals Page
- [`web-market`](../../components/electric-wealth-web/web-market.md) — Market Pages
- [`web-education`](../../components/electric-wealth-web/web-education.md) — Learning Centre Pages
- [`web-advisor`](../../components/electric-wealth-web/web-advisor.md) — Advisor Pages
- [`web-notifications`](../../components/electric-wealth-web/web-notifications.md) — Notifications Page and Bell
- [`web-profile`](../../components/electric-wealth-web/web-profile.md) — Profile Page
- [`web-settings`](../../components/electric-wealth-web/web-settings.md) — Settings Page
- [`web-app-shell`](../../components/electric-wealth-web/web-app-shell.md) — App Shell, Routing and Layout
- [`web-api-client`](../../components/electric-wealth-web/web-api-client.md) — Typed API Client and Query Hooks
- [`web-design-system`](../../components/electric-wealth-web/web-design-system.md) — UI Components, Charts and Styles
- [`web-i18n-and-formatting`](../../components/electric-wealth-web/web-i18n-and-formatting.md) — Internationalisation and Formatting
