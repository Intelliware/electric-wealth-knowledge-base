---
component_id: web-education
repo: web
type: ui-feature
description: >-
  Use when the user browses lessons by level, marks a lesson complete, or searches the glossary at /learn or /learn/glossary.
triggers:
  - "learn page"
  - "lessons"
  - "mark complete"
  - "glossary page"
  - "glossary search"
paths:
  - "src/features/education/**"
depends_on:
  - "api-education"
  - "web-api-client"
  - "web-design-system"
  - "web-i18n-and-formatting"
consumed_by: []
data_entities:
  - "EducationContent"
  - "EducationProgress"
  - "GlossaryTerm"
last_verified_against_commit: 7489670e43b9b3498218e9a1559254ff6a581ea3
last_feature_id: ""
source_doc: docs/web-app-internal/13-feature-modules.md
verification_signal: route-inventory-diff
---

# Learning Centre Pages

> **Repo:** `web` ([repo summary](../../repos/electric-wealth-web/README.md)) · **Type:** `ui-feature` · **Source doc:** [`docs/web-app-internal/13-feature-modules.md`](../../../electric-wealth-web/docs/web-app-internal/13-feature-modules.md)

## 1. Overview

`LearnPage` filters lessons by level and shows completion; "Mark complete" always sends `progressPct: 100` (there is no lesson viewer). `GlossaryPage` has a deferred search box. Both queries are keyed by locale.

**Routes:** `/learn`, `/learn/glossary`

**Depends on:** [`api-education`](../../components/electric-wealth-api-spring/api-education.md), [`web-api-client`](../../components/electric-wealth-web/web-api-client.md), [`web-design-system`](../../components/electric-wealth-web/web-design-system.md), [`web-i18n-and-formatting`](../../components/electric-wealth-web/web-i18n-and-formatting.md)

**Shared entities:** [`EducationContent`](../../shared/entities/education-content.md), [`EducationProgress`](../../shared/entities/education-progress.md), [`GlossaryTerm`](../../shared/entities/glossary-term.md)

---

## 2. Key Responsibilities

- List lessons and completion.
- Mark lessons complete.
- Search the glossary.

**Explicitly NOT in scope:**

- Bilingual content selection — owned by [`api-education`](../../components/electric-wealth-api-spring/api-education.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Routes `/learn`, `/learn/glossary` | Bookmarks, `returnTo` redirects, dashboard deep-link mapping, navigation in `web-app-shell` | `route-inventory-diff` against `src/App.tsx`, then `npm run verify` |
| Internal refactor (no interface change) | This feature folder only | `npm run typecheck` (no automated UI tests exist) |
| Data-entity schema change | API schema change in `api-education` surfaces as type errors here | `npm run gen:api` then `npm run verify` |
| Dependency upgrade | Whole SPA bundle | `npm run build` |

**Upstream dependents to notify:** None modelled.

**Downstream services to monitor post-change:** `api-education`, `web-api-client`, `web-design-system`, `web-i18n-and-formatting`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `7489670e43b9b3498218e9a1559254ff6a581ea3` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
