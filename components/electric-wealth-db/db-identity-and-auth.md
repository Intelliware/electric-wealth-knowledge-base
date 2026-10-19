---
component_id: db-identity-and-auth
repo: db
type: database-schema
description: >-
  Use when a change touches the users, user_profiles, user_preferences or sessions tables — logins, demo credentials, profile/address columns, preference flags, or bearer-session rows shared by the Spring and Node APIs.
triggers:
  - "users table"
  - "sessions table"
  - "user profile columns"
  - "user preferences columns"
  - "login email"
  - "mfa method"
  - "session expiry"
  - "advisor assignment"
paths:
  - "migrations/001_initial_schema.sql"
  - "migrations/005_advisor.sql"
  - "migrations/007_registration.sql"
  - "seed/00[1-6]_*.sql"
depends_on:
  - "db-advisor"
consumed_by:
  - "api-advisor"
  - "api-auth-sessions"
  - "api-profile-and-preferences"
  - "api-registration"
  - "db-accounts-and-investments"
  - "db-advisor"
  - "db-documents"
  - "db-goals-and-dashboard"
  - "db-learning-centre"
  - "db-market-data"
  - "db-notifications"
  - "db-online-enrolment"
data_entities:
  - "User"
  - "Session"
  - "UserProfile"
  - "UserPreferences"
last_verified_against_commit: e6262fcddca7f4c0488248ca8881483d9b920f0a
last_feature_id: ""
source_doc: docs/database-internal/05-tables-detailed-documentation.md
verification_signal: schema-diff
---

# Identity and Auth Schema

> **Repo:** `db` ([repo summary](../../repos/electric-wealth-db/README.md)) · **Type:** `database-schema` · **Source doc:** [`docs/database-internal/05-tables-detailed-documentation.md`](../../../electric-wealth-db/docs/database-internal/05-tables-detailed-documentation.md)

## 1. Overview

Owns the identity hub of the schema. `users` is referenced by 18 tables (16 with `ON DELETE CASCADE`); `user_profiles` and `user_preferences` are 1:0..1 extensions keyed by `user_id`; `sessions` stores opaque bearer/refresh token pairs shared by the Spring API and the Node API.

Introduced by migration 001; migration 005 adds `users.advisor_id` (FK → `advisors`) and migration 007 adds `users.mfa_method` and `users.terms_accepted_at`. Demo-only plain-text `password` and `mfa_code` columns are an explicit design choice of this workshop dataset.

Consumed (via direct SQL) by `api-auth-sessions`, `api-profile-and-preferences`, `api-registration`, `api-advisor` and `api-request-pipeline` (session lookup on every protected request).

**Tables:** `users`, `user_profiles`, `user_preferences`, `sessions`

**Depends on:** [`db-advisor`](../../components/electric-wealth-db/db-advisor.md)

**Consumed by:** [`api-advisor`](../../components/electric-wealth-api-spring/api-advisor.md), [`api-auth-sessions`](../../components/electric-wealth-api-spring/api-auth-sessions.md), [`api-profile-and-preferences`](../../components/electric-wealth-api-spring/api-profile-and-preferences.md), [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md), [`db-accounts-and-investments`](../../components/electric-wealth-db/db-accounts-and-investments.md), [`db-advisor`](../../components/electric-wealth-db/db-advisor.md), [`db-documents`](../../components/electric-wealth-db/db-documents.md), [`db-goals-and-dashboard`](../../components/electric-wealth-db/db-goals-and-dashboard.md), [`db-learning-centre`](../../components/electric-wealth-db/db-learning-centre.md), [`db-market-data`](../../components/electric-wealth-db/db-market-data.md), [`db-notifications`](../../components/electric-wealth-db/db-notifications.md), [`db-online-enrolment`](../../components/electric-wealth-db/db-online-enrolment.md)

**Shared entities:** [`User`](../../shared/entities/user.md), [`Session`](../../shared/entities/session.md), [`UserProfile`](../../shared/entities/user-profile.md), [`UserPreferences`](../../shared/entities/user-preferences.md)

---

## 2. Key Responsibilities

- Define login identity (`users.email` unique, `COLLATE NOCASE`) and demo credentials.
- Store profile/address and trusted-contact columns (`user_profiles`) and preference flags (`user_preferences`, no defaults — inserts must supply every column).
- Store bearer sessions (`sessions`: access/refresh token, `mfa_verified`, `revoked`, `expires_at`); rows are never deleted.
- Anchor cascading deletes for every user-owned table.

**Explicitly NOT in scope:**

- Session issuance, MFA and expiry rules — owned by [`api-auth-sessions`](../../components/electric-wealth-api-spring/api-auth-sessions.md)
- Online enrolment staging tables — owned by [`db-online-enrolment`](../../components/electric-wealth-db/db-online-enrolment.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Tables `users`, `user_profiles`, `user_preferences`, `sessions` | Direct SQL readers `api-advisor`, `api-auth-sessions`, `api-profile-and-preferences`, `api-registration`, `db-accounts-and-investments`, `db-advisor`, `db-documents`, `db-goals-and-dashboard`, `db-learning-centre`, `db-market-data`, `db-notifications`, `db-online-enrolment`; Node API (`electric-wealth-api`) shares the same file | `schema-diff` (data dictionary vs `pragma_table_info`), then `./mvnw test` in `electric-wealth-api-spring` |
| Internal refactor (no interface change) | Seed data / indexes only | `npm run db:reset` then `npm run db:status` |
| Data-entity schema change | New migration required; every API repository touching these tables | Part A § 20 new-table checklist + API test suite |
| Dependency upgrade | Node `node:sqlite` / SQLite version affects all schema components | `npm run db:reset` + API test suite |

**Upstream dependents to notify:** `api-advisor`, `api-auth-sessions`, `api-profile-and-preferences`, `api-registration`, `db-accounts-and-investments`, `db-advisor`, `db-documents`, `db-goals-and-dashboard`, `db-learning-centre`, `db-market-data`, `db-notifications`, `db-online-enrolment` Also the Node API (`electric-wealth-api`, not in this workspace), which shares the database file.

**Downstream services to monitor post-change:** `db-advisor`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `e6262fcddca7f4c0488248ca8881483d9b920f0a` (2026-10-19)

**Open follow-ups from extraction:**

- `sessions` rows are never deleted — unbounded growth noted in Part A § 17 (retention).
- Plain-text demo secrets (`users.password`, `users.mfa_code`) must never be carried into a real deployment.
