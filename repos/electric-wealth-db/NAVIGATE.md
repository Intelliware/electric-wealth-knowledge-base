# NAVIGATE — `db` (electric-wealth-db)

**Role and primary tech:** Database — SQLite schema, migrations and seed data built by Node.js 24 scripts.

When the user's task touches this repo, read [`README.md`](README.md) first (ownership, tech stack, public
interfaces), then walk [`components/electric-wealth-db/`](../../components/electric-wealth-db/) and follow `consumed_by` /
`depends_on` outwards (or query [`graph.json`](../../graph.json)). Do not read every component — pick by
`triggers` and `description`, default cap 7.

Source repo: `../electric-wealth-db` at commit `e6262fcddca7f4c0488248ca8881483d9b920f0a`. Part A (internal) docs live in the source repo under
`docs/database-internal/`; Part B (consumer) docs live in this KB under `source-docs/electric-wealth-db/database-consumer/`.

## Components in this repo

- [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md) — Identity and Auth Schema
- [`db-accounts-and-investments`](../../components/electric-wealth-db/db-accounts-and-investments.md) — Accounts and Investments Schema
- [`db-documents`](../../components/electric-wealth-db/db-documents.md) — Documents Schema
- [`db-goals-and-dashboard`](../../components/electric-wealth-db/db-goals-and-dashboard.md) — Goals and Dashboard Schema
- [`db-notifications`](../../components/electric-wealth-db/db-notifications.md) — Notifications Schema
- [`db-learning-centre`](../../components/electric-wealth-db/db-learning-centre.md) — Learning Centre Schema
- [`db-advisor`](../../components/electric-wealth-db/db-advisor.md) — Advisor Schema
- [`db-market-data`](../../components/electric-wealth-db/db-market-data.md) — Market Data Schema
- [`db-online-enrolment`](../../components/electric-wealth-db/db-online-enrolment.md) — Online Enrolment Schema
- [`db-migration-and-seed-tooling`](../../components/electric-wealth-db/db-migration-and-seed-tooling.md) — Migration and Seed Tooling
