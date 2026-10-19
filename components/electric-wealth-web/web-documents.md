---
component_id: web-documents
repo: web
type: ui-feature
description: >-
  Use when the user filters, downloads or shares documents on /documents, including the ?highlight= deep-link row.
triggers:
  - "documents page"
  - "document vault"
  - "download PDF"
  - "share document dialog"
  - "highlight document"
  - "document filters"
paths:
  - "src/features/documents/**"
depends_on:
  - "api-documents"
  - "web-api-client"
  - "web-design-system"
  - "web-i18n-and-formatting"
consumed_by: []
data_entities:
  - "Document"
  - "DocumentShare"
last_verified_against_commit: 7489670e43b9b3498218e9a1559254ff6a581ea3
last_feature_id: ""
source_doc: docs/web-app-internal/13-feature-modules.md
verification_signal: route-inventory-diff
---

# Documents Page

> **Repo:** `web` ([repo summary](../../repos/electric-wealth-web/README.md)) · **Type:** `ui-feature` · **Source doc:** [`docs/web-app-internal/13-feature-modules.md`](../../../electric-wealth-web/docs/web-app-internal/13-feature-modules.md)

## 1. Overview

Client-side name search plus server-side `year` / `category` filters (`pageSize: 100`). Download fetches the base64 stub and saves it through a temporary `<a download>`; `ShareDialog` is a native `<dialog>` that posts the recipient email and shows the link expiry.

**Routes:** `/documents`

**Depends on:** [`api-documents`](../../components/electric-wealth-api-spring/api-documents.md), [`web-api-client`](../../components/electric-wealth-web/web-api-client.md), [`web-design-system`](../../components/electric-wealth-web/web-design-system.md), [`web-i18n-and-formatting`](../../components/electric-wealth-web/web-i18n-and-formatting.md)

**Shared entities:** [`Document`](../../shared/entities/document.md), [`DocumentShare`](../../shared/entities/document-share.md)

---

## 2. Key Responsibilities

- Filter and list documents.
- Download the stub PDF.
- Share a document by email.
- Highlight a deep-linked row.

**Explicitly NOT in scope:**

- Document storage and share TTL — owned by [`api-documents`](../../components/electric-wealth-api-spring/api-documents.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Routes `/documents` | Bookmarks, `returnTo` redirects, dashboard deep-link mapping, navigation in `web-app-shell` | `route-inventory-diff` against `src/App.tsx`, then `npm run verify` |
| Internal refactor (no interface change) | This feature folder only | `npm run typecheck` (no automated UI tests exist) |
| Data-entity schema change | API schema change in `api-documents` surfaces as type errors here | `npm run gen:api` then `npm run verify` |
| Dependency upgrade | Whole SPA bundle | `npm run build` |

**Upstream dependents to notify:** None modelled.

**Downstream services to monitor post-change:** `api-documents`, `web-api-client`, `web-design-system`, `web-i18n-and-formatting`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `7489670e43b9b3498218e9a1559254ff6a581ea3` (2026-10-19)

**Open follow-ups from extraction:**

- Download failures are unhandled (no `catch`), so the user sees no error (Part A § 8.3).
