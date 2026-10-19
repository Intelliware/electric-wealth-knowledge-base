---
component_id: api-documents
repo: api-spring
type: api-feature
description: >-
  Use when a client lists, downloads or shares documents through /api/wealth/documents.
triggers:
  - "documents"
  - "download document"
  - "share document"
  - "tax slips"
  - "statements"
  - "document filters"
paths:
  - "src/main/java/com/electricwealth/api/web/DocumentController.java"
  - "src/main/java/com/electricwealth/api/dto/DocumentDtos.java"
  - "src/main/java/com/electricwealth/api/repository/DocumentRepository.java"
  - "src/test/java/com/electricwealth/api/WealthApiTest.java"
depends_on:
  - "db-documents"
  - "api-request-pipeline"
  - "api-contract-and-data-access"
consumed_by:
  - "web-documents"
data_entities:
  - "Document"
  - "DocumentShare"
last_verified_against_commit: 3f7403e33d97b39872e89690431c7880dea98ae4
last_feature_id: ""
source_doc: docs/api-internal/12-domain-models-and-business-rules.md
verification_signal: openapi-diff
---

# Documents

> **Repo:** `api-spring` ([repo summary](../../repos/electric-wealth-api-spring/README.md)) · **Type:** `api-feature` · **Source doc:** [`docs/api-internal/12-domain-models-and-business-rules.md`](../../../electric-wealth-api-spring/docs/api-internal/12-domain-models-and-business-rules.md)

## 1. Overview

Lists documents (`year DESC, action_required DESC, id`) with optional exact `year` / `category` filters, returns a generated one-page stub PDF as base64 on detail, and records a share row that expires after 7 days.

**Operations:** `GET /api/wealth/documents`, `GET /api/wealth/documents/{id}`, `POST /api/wealth/documents/{id}/share`

**Depends on:** [`db-documents`](../../components/electric-wealth-db/db-documents.md), [`api-request-pipeline`](../../components/electric-wealth-api-spring/api-request-pipeline.md), [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md)

**Consumed by:** [`web-documents`](../../components/electric-wealth-web/web-documents.md)

**Shared entities:** [`Document`](../../shared/entities/document.md), [`DocumentShare`](../../shared/entities/document-share.md)

---

## 2. Key Responsibilities

- Filter and page document metadata.
- Return a downloadable stub PDF.
- Record document shares with a 7-day TTL.

**Explicitly NOT in scope:**

- Real document storage or email delivery (not implemented) — owned by [`api-documents`](../../components/electric-wealth-api-spring/api-documents.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| HTTP operations (3) in `openapi.json`, tag `Documents` | Web callers `web-documents`; web `src/api/schema.d.ts` must be regenerated; Node API parity | `openapi-diff`: `OpenApiSpecTest`, `ContractParityTest`, then `npm run verify` in `electric-wealth-web` |
| Internal refactor (no interface change) | This component's controller / repository only | `./mvnw test -Dtest=WealthApiTest` |
| Data-entity schema change | Schema owner(s) `db-documents` and every API feature reading the same tables | `schema-diff` on `electric-wealth-db` + full `./mvnw test` |
| Dependency upgrade | Whole API (single Maven module) | `./mvnw verify` incl. `OpenApiSpecTest` |

**Upstream dependents to notify:** `web-documents`

**Downstream services to monitor post-change:** `db-documents`, `api-request-pipeline`, `api-contract-and-data-access`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (2026-10-19)

**Open follow-ups from extraction:**

- Share sends no email and download returns a stub PDF — demo behaviour (Part A § 12.4).
