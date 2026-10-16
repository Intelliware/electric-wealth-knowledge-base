---
slug: how-to-query-this-database
title: "How to Query This Database"
part: B2
source-repo: electric-wealth-db
source-commit: 05ff1a61e87f62666c0fceb35fc1604c1d7abccd
schema-version: 007_registration.sql
extracted: 2026-10-15
---

## 19. How to Query This Database

The Electric Wealth database is a **single SQLite 3 file** owned by the `electric-wealth-db` repo. Application code reaches it in one of two ways:

- **In the product:** through the Spring API (`electric-wealth-api-spring`). The web app never touches the database; it calls the REST API.
- **For development and inspection:** directly, with the sqlite3 CLI, DBeaver or `node:sqlite`.

The schema (34 tables) is described in the [data dictionary](../data-dictionary.md) and the [ERD](../erd.mmd).

### 19.1 Connection Setup

**Build the file first** (Node.js 24 required; no `npm install`):
```bash
cd electric-wealth-db
npm run db:reset      # creates data/electric-wealth.db from migrations + seed
npm run db:status     # shows applied migrations and row counts
```

**File location:** `electric-wealth-db/data/electric-wealth.db`. Override with `DB_PATH=<path>` (scripts and API) or `--db <path>` (scripts). There are no credentials.

**Java / Spring Boot (how the API connects):**
```yaml
# application.yml
spring.datasource.hikari.maximum-pool-size: 4
ew.db-path: ${DB_PATH:../electric-wealth-db/data/electric-wealth.db}
```
```java
// DatabaseConfig.java builds the JDBC URL:
"jdbc:sqlite:" + absolutePath + "?foreign_keys=true&busy_timeout=3000"
// Dependency: org.xerial:sqlite-jdbc 3.53.4.0. Queries use Spring's JdbcClient.
```

**Node.js:**
```js
import { DatabaseSync } from 'node:sqlite';
const db = new DatabaseSync(process.env.DB_PATH ?? '../electric-wealth-db/data/electric-wealth.db');
db.exec('PRAGMA foreign_keys = ON;');
```

**SQL (command line):**
```bash
sqlite3 electric-wealth-db/data/electric-wealth.db -cmd "PRAGMA foreign_keys=ON;"
```

### 19.2 Rules Every Consumer Must Follow

1. **Turn foreign keys on for every connection** (`PRAGMA foreign_keys = ON` or `?foreign_keys=true`). SQLite defaults to OFF, and without it `ON DELETE CASCADE` does nothing.
2. **Scope every query on user data by `user_id`.** Ids like `'tfsa'`, `'d1'` and `'goal-1'` are unique **per user only**, and the database has no row-level security.
3. **Money is INTEGER minor units** (cents) next to a `currency` column. Convert only for display.
4. **Booleans are INTEGER 0/1.** Bind `1`/`0`, not `true`/`false` strings.
5. **Timestamps are ISO-8601 UTC TEXT**, except `appointments.date` + `time`, which are Toronto local time.
6. **Do not run DDL from application code.** Schema changes go through `migrations/` in the db repo.
7. **Localised catalogue text** comes in `_en`/`_fr` column pairs. Select the one that matches the request language.

### 19.3 Demo Data Available

Six personas with password `Demo123!` and MFA code `123456`:

| `users.id` | Email | Accounts |
|---|---|---|
| `sarah` | `sarah.chen@example.com` | TFSA, RRSP, FHSA, non-registered |
| `michael` | `michael.thompson@example.com` | TFSA, RRSP, 2 × RESP, non-registered |
| `linda` | `linda.rodriguez@example.com` | TFSA, RRSP, LIRA, RRIF, non-registered |
| `david` | `david.kim@example.com` | TFSA, RRSP, RDSP, 2 × non-registered |
| `emma` | `emma.wilson@example.com` | TFSA, RRSP, FHSA, non-registered |
| `foster` | `robert.foster@example.com` | TFSA, 2 × RRIF, non-registered |

Two branch clients are ready to enrol online. Identity is checked on account number + postal code + date of birth:

| Account number | Name | Postal code | Date of birth |
|---|---|---|---|
| `7391046285` | Priya Patel | `K1P 5G4` | `1990-02-17` |
| `2654819370` | Marc Tremblay | `H2X 1Y4` | `1985-09-03` |
