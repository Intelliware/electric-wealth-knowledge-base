---
component_id: db-documents
repo: db
type: database-schema
description: >-
  Use when a change touches stored client documents or document share records — the documents and document_shares tables, their composite keys, or seeded statements and tax slips.
triggers:
  - "documents table"
  - "document shares"
  - "share expiry"
  - "tax documents"
  - "statements"
  - "action required flag"
paths:
  - "migrations/001_initial_schema.sql"
  - "migrations/003_document_shares.sql"
  - "seed/00[1-6]_*.sql"
depends_on:
  - "db-identity-and-auth"
consumed_by:
  - "api-documents"
data_entities:
  - "Document"
  - "DocumentShare"
last_verified_against_commit: e6262fcddca7f4c0488248ca8881483d9b920f0a
last_feature_id: ""
source_doc: docs/database-internal/05-tables-detailed-documentation.md
verification_signal: schema-diff
---

# Documents Schema

> **Repo:** `db` ([repo summary](../../repos/electric-wealth-db/README.md)) · **Type:** `database-schema` · **Source doc:** [`docs/database-internal/05-tables-detailed-documentation.md`](../../../electric-wealth-db/docs/database-internal/05-tables-detailed-documentation.md)

## 1. Overview

Stores per-user document metadata (`documents`, composite key `(user_id, id)`) and email-share audit rows (`document_shares`, AUTOINCREMENT id, introduced by migration 003). No binary content is stored — the API generates a stub PDF on download.

Documents are seed-only; shares are inserted by `api-documents` with a 7-day `expires_at`.

**Tables:** `documents`, `document_shares`

**Depends on:** [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md)

**Consumed by:** [`api-documents`](../../components/electric-wealth-api-spring/api-documents.md)

**Shared entities:** [`Document`](../../shared/entities/document.md), [`DocumentShare`](../../shared/entities/document-share.md)

---

## 2. Key Responsibilities

- Define document metadata columns (category, year, size, action-required flag, optional account label).
- Record each share (recipient, created/expiry timestamps) with cascade from the parent document.

**Explicitly NOT in scope:**

- Document download rendering and share TTL — owned by [`api-documents`](../../components/electric-wealth-api-spring/api-documents.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Tables `documents`, `document_shares` | Direct SQL readers `api-documents`; Node API (`electric-wealth-api`) shares the same file | `schema-diff` (data dictionary vs `pragma_table_info`), then `./mvnw test` in `electric-wealth-api-spring` |
| Internal refactor (no interface change) | Seed data / indexes only | `npm run db:reset` then `npm run db:status` |
| Data-entity schema change | New migration required; every API repository touching these tables | Part A § 20 new-table checklist + API test suite |
| Dependency upgrade | Node `node:sqlite` / SQLite version affects all schema components | `npm run db:reset` + API test suite |

**Upstream dependents to notify:** `api-documents` Also the Node API (`electric-wealth-api`, not in this workspace), which shares the database file.

**Downstream services to monitor post-change:** `db-identity-and-auth`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `e6262fcddca7f4c0488248ca8881483d9b920f0a` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
