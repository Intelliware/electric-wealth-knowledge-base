---
component_id: db-migration-and-seed-tooling
repo: db
type: database-schema
description: >-
  Use when a change adds or alters a migration, seed file or the Node scripts that build, reset and report on the SQLite database file.
triggers:
  - "add migration"
  - "db:migrate"
  - "db:seed"
  - "db:reset"
  - "db:status"
  - "schema_migrations"
  - "new table checklist"
  - "node:sqlite"
paths:
  - "scripts/**"
  - "package.json"
  - "migrations/**"
  - "seed/**"
depends_on: []
consumed_by: []
data_entities:
  - "SchemaMigration"
last_verified_against_commit: e6262fcddca7f4c0488248ca8881483d9b920f0a
last_feature_id: ""
source_doc: docs/database-internal/15-migrations-and-versioning.md
verification_signal: manual
---

# Migration and Seed Tooling

> **Repo:** `db` ([repo summary](../../repos/electric-wealth-db/README.md)) · **Type:** `database-schema` · **Source doc:** [`docs/database-internal/15-migrations-and-versioning.md`](../../../electric-wealth-db/docs/database-internal/15-migrations-and-versioning.md)

## 1. Overview

Node.js 24 scripts using the built-in `node:sqlite` module (`scripts/lib.js`) apply `migrations/NNN_*.sql` in order, record them in `schema_migrations`, and load `seed/*.sql` (six persona files 001–006 plus domain seeds 102–107). `npm run db:reset` rebuilds `data/electric-wealth.db` from scratch.

The Spring API never creates or migrates tables (`DatabaseConfig`), so this tooling is the only path for schema change. It has no runtime dependants in the graph; every schema component's DDL flows through it.

**Tables:** `schema_migrations`

**Local entities (not promoted):** `SchemaMigration`

---

## 2. Key Responsibilities

- Apply pending migrations in order and record them in `schema_migrations`.
- Seed the six demo personas and domain data.
- Rebuild and report on the local database file.

**Explicitly NOT in scope:**

- Runtime connection settings (path, pool size) — owned by [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Tables `schema_migrations` | Direct SQL readers none modelled; Node API (`electric-wealth-api`) shares the same file | `schema-diff` (data dictionary vs `pragma_table_info`), then `./mvnw test` in `electric-wealth-api-spring` |
| Internal refactor (no interface change) | Seed data / indexes only | `npm run db:reset` then `npm run db:status` |
| Data-entity schema change | New migration required; every API repository touching these tables | Part A § 20 new-table checklist + API test suite |
| Dependency upgrade | Node `node:sqlite` / SQLite version affects all schema components | `npm run db:reset` + API test suite |

**Upstream dependents to notify:** None modelled. Also the Node API (`electric-wealth-api`, not in this workspace), which shares the database file.

**Downstream services to monitor post-change:** None.

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `e6262fcddca7f4c0488248ca8881483d9b920f0a` (2026-10-19)

**Open follow-ups from extraction:**

- No backup or staging environment exists; the database file is git-ignored and rebuilt locally (Part A § 16).
