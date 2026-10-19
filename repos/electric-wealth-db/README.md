# Electric Wealth Database — Repository Summary

> **Repo code:** `db`
> **Role:** database
> **Type:** sqlite-node-sql-migrations
> **Path in source tree:** `{project-root}/../electric-wealth-db`
> **Primary:** no
> **Last indexed commit:** e6262fcddca7f4c0488248ca8881483d9b920f0a

This page is the per-repository entry point inside the Electric Wealth knowledge base. The
[`ARCHITECTURE.md`](../../ARCHITECTURE.md) Repository Map (§ 2) links here. KB folder slug: `electric-wealth-db`.

---

## 1. Purpose

The SQLite schema and demo data for Electric Wealth: 34 application tables in nine domains, built from seven SQL migrations plus seed data for six personas by Node.js scripts. It owns every DDL change; the Spring API (and the out-of-workspace Node API) read and write the file but never migrate it. It holds no business logic (no views, triggers or procedures) and is local/workshop only.

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
| Language(s) | SQL (SQLite 3), JavaScript (Node.js 24 scripts) |  |
| Framework(s) | Built-in `node:sqlite` | No migration framework |
| Build / package manager | npm scripts `db:migrate`, `db:seed`, `db:reset`, `db:status` |  |
| Test stack | None | Verified by RE parity scripts only |
| Datastore(s) | Single file `data/electric-wealth.db` (git-ignored) |  |
| Runtime / deploy target | Developer machine | No backup, no staging/production |
| Key dependencies (top 5–10) | Node.js 24 built-ins only |  |

---

## 4. Top Components

Full list: Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).

| Component ID | Type | Description (Use-when …) | Doc |
|---|---|---|---|
| [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md) | `database-schema` | Use when a change touches the users, user_profiles, user_preferences or sessions tables — logins, demo credentials, profile/address columns, preference flags, or bearer-session rows shared by the Spring and Node APIs. | [doc](../../components/electric-wealth-db/db-identity-and-auth.md) |
| [`db-accounts-and-investments`](../../components/electric-wealth-db/db-accounts-and-investments.md) | `database-schema` | Use when a change touches investment accounts, holdings, transactions or account performance data — columns, composite (user_id, account_id) keys, money-in-cents conventions or seeded portfolio data. | [doc](../../components/electric-wealth-db/db-accounts-and-investments.md) |
| [`db-goals-and-dashboard`](../../components/electric-wealth-db/db-goals-and-dashboard.md) | `database-schema` | Use when a change touches savings goals or dashboard content tables — goals, portfolio_snapshots, asset_allocations, alerts or quick_links. | [doc](../../components/electric-wealth-db/db-goals-and-dashboard.md) |
| [`db-online-enrolment`](../../components/electric-wealth-db/db-online-enrolment.md) | `database-schema` | Use when a change touches online enrolment storage — branch client records, security questions, draft registrations, or the security answers saved during and after registration. | [doc](../../components/electric-wealth-db/db-online-enrolment.md) |
| [`db-advisor`](../../components/electric-wealth-db/db-advisor.md) | `database-schema` | Use when a change touches advisors, advisor appointments or secure advisor messages — including the partial unique slot index that prevents double booking. | [doc](../../components/electric-wealth-db/db-advisor.md) |
| [`db-market-data`](../../components/electric-wealth-db/db-market-data.md) | `database-schema` | Use when a change touches static market data or watchlists — securities, price history, news, weekly roundup items, thematic baskets or the per-user watchlist table. | [doc](../../components/electric-wealth-db/db-market-data.md) |
| [`db-migration-and-seed-tooling`](../../components/electric-wealth-db/db-migration-and-seed-tooling.md) | `database-schema` | Use when a change adds or alters a migration, seed file or the Node scripts that build, reset and report on the SQLite database file. | [doc](../../components/electric-wealth-db/db-migration-and-seed-tooling.md) |

---

## 5. Public Interfaces

| Kind | Identifier | Description | Source of Truth |
|---|---|---|---|
| `database-table` | `users` | Identity and Auth Schema | `migrations/` |
| `database-table` | `user_profiles` | Identity and Auth Schema | `migrations/` |
| `database-table` | `user_preferences` | Identity and Auth Schema | `migrations/` |
| `database-table` | `sessions` | Identity and Auth Schema | `migrations/` |
| `database-table` | `accounts` | Accounts and Investments Schema | `migrations/` |
| `database-table` | `holdings` | Accounts and Investments Schema | `migrations/` |
| `database-table` | `transactions` | Accounts and Investments Schema | `migrations/` |
| `database-table` | `account_performance` | Accounts and Investments Schema | `migrations/` |
| `database-table` | `performance_points` | Accounts and Investments Schema | `migrations/` |
| `database-table` | `documents` | Documents Schema | `migrations/` |
| `database-table` | `document_shares` | Documents Schema | `migrations/` |
| `database-table` | `goals` | Goals and Dashboard Schema | `migrations/` |
| `database-table` | `portfolio_snapshots` | Goals and Dashboard Schema | `migrations/` |
| `database-table` | `asset_allocations` | Goals and Dashboard Schema | `migrations/` |
| `database-table` | `alerts` | Goals and Dashboard Schema | `migrations/` |
| `database-table` | `quick_links` | Goals and Dashboard Schema | `migrations/` |
| `database-table` | `notifications` | Notifications Schema | `migrations/` |
| `database-table` | `education_content` | Learning Centre Schema | `migrations/` |
| `database-table` | `education_progress` | Learning Centre Schema | `migrations/` |
| `database-table` | `glossary_terms` | Learning Centre Schema | `migrations/` |
| `database-table` | `advisors` | Advisor Schema | `migrations/` |
| `database-table` | `appointments` | Advisor Schema | `migrations/` |
| `database-table` | `advisor_messages` | Advisor Schema | `migrations/` |
| `database-table` | `securities` | Market Data Schema | `migrations/` |
| `database-table` | `price_history` | Market Data Schema | `migrations/` |
| `database-table` | `watchlist` | Market Data Schema | `migrations/` |
| `database-table` | `market_news` | Market Data Schema | `migrations/` |
| `database-table` | `market_roundup_items` | Market Data Schema | `migrations/` |
| `database-table` | `baskets` | Market Data Schema | `migrations/` |
| `database-table` | `offline_clients` | Online Enrolment Schema | `migrations/` |
| `database-table` | `security_questions` | Online Enrolment Schema | `migrations/` |
| `database-table` | `registrations` | Online Enrolment Schema | `migrations/` |
| `database-table` | `registration_security_answers` | Online Enrolment Schema | `migrations/` |
| `database-table` | `user_security_answers` | Online Enrolment Schema | `migrations/` |
| `database-table` | `schema_migrations` | Migration and Seed Tooling | `migrations/` |
| `cli-command` | `npm run db:migrate` | Migration and seed tooling | `package.json` |
| `cli-command` | `npm run db:seed` | Migration and seed tooling | `package.json` |
| `cli-command` | `npm run db:reset` | Migration and seed tooling | `package.json` |
| `cli-command` | `npm run db:status` | Migration and seed tooling | `package.json` |

---

## 6. Internal & Consumer Doc Pointers

Part A paths are relative to the source repo's root; Part B lives in this KB under `source-docs/` (KB-relative path shown).

| Document | Audience | Location |
|---|---|---|
| Part A — Internal docs | Maintainers of db | [`docs/database-internal/`](../../../electric-wealth-db/docs/database-internal/01-database-overview.md) (source repo) |
| Part B — Consumer / Implementer docs | Downstream callers | [`source-docs/electric-wealth-db/database-consumer/`](../../source-docs/electric-wealth-db/database-consumer/) (KB) |
| API contract (OpenAPI / AsyncAPI / GraphQL SDL) | Tooling, integration tests | [`migrations/`](../../../electric-wealth-db/migrations/) (DDL); KB data dictionary [`source-docs/electric-wealth-db/data-dictionary.md`](../../source-docs/electric-wealth-db/data-dictionary.md) and ERD [`erd.mmd`](../../source-docs/electric-wealth-db/erd.mmd) |
| Architecture Decision Records (repo-local) | Maintainers, architects | |
| Runbooks / on-call playbooks | On-call engineers | |
| Onboarding guide | New contributors | [`README.md`](../../../electric-wealth-db/README.md) |
| RE verification report | KB maintainers | [`docs/database-internal/verification-report.md`](../../../electric-wealth-db/docs/database-internal/verification-report.md) — `MECHANICAL: GO` |
