# NAVIGATE — `api-spring` (electric-wealth-api-spring)

**Role and primary tech:** REST API — Java 21 / Spring Boot 4.1.1, plain SQL over SQLite.

When the user's task touches this repo, read [`README.md`](README.md) first (ownership, tech stack, public
interfaces), then walk [`components/electric-wealth-api-spring/`](../../components/electric-wealth-api-spring/) and follow `consumed_by` /
`depends_on` outwards (or query [`graph.json`](../../graph.json)). Do not read every component — pick by
`triggers` and `description`, default cap 7.

Source repo: `../electric-wealth-api-spring` at commit `3f7403e33d97b39872e89690431c7880dea98ae4`. Part A (internal) docs live in the source repo under
`docs/api-internal/`; Part B (consumer) docs live in this KB under `source-docs/electric-wealth-api-spring/api-consumer/`.

## Components in this repo

- [`api-health`](../../components/electric-wealth-api-spring/api-health.md) — Health Endpoint
- [`api-auth-sessions`](../../components/electric-wealth-api-spring/api-auth-sessions.md) — Auth and Sessions
- [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md) — Online Registration
- [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md) — Dashboard
- [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md) — Accounts
- [`api-goals`](../../components/electric-wealth-api-spring/api-goals.md) — Savings Goals
- [`api-documents`](../../components/electric-wealth-api-spring/api-documents.md) — Documents
- [`api-profile-and-preferences`](../../components/electric-wealth-api-spring/api-profile-and-preferences.md) — Profile and Preferences
- [`api-notifications`](../../components/electric-wealth-api-spring/api-notifications.md) — Notifications
- [`api-advisor`](../../components/electric-wealth-api-spring/api-advisor.md) — Advisor Booking and Messaging
- [`api-education`](../../components/electric-wealth-api-spring/api-education.md) — Education
- [`api-market`](../../components/electric-wealth-api-spring/api-market.md) — Market Data and Watchlist
- [`api-request-pipeline`](../../components/electric-wealth-api-spring/api-request-pipeline.md) — Request Pipeline, Security and Errors
- [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md) — OpenAPI Contract and Data Access Foundation
