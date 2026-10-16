---
title: "Filtering, Sorting, and Search"
block: reverse-eng/api/consumer/filtering-sorting-search
part: B2
source_repo: electric-wealth-api-spring
source_version: 0.1.0
source_commit: 8a61cdeafb4537b16221d7aa0e9300010abd3387
extracted: 2026-10-15
---

## 9. Filtering, Sorting, and Search

**Filter conventions:**

Plain query parameters with exact-match semantics; combine with AND. Omitted or empty → no filter.

**Filterable fields per resource:**

| Endpoint | Parameter | Type / values | Semantics |
|---|---|---|---|
| `GET /api/wealth/accounts/{id}/transactions` | `type` | `contribution`, `buy`, `sell`, `dividend`, `fee`, `withdrawal`, `transfer`, `scheduled` | Posted transactions of that type (`scheduled` list unaffected) |
| `GET /api/wealth/documents` | `year` | integer | Exact |
| `GET /api/wealth/documents` | `category` | string (e.g. `Tax`) | Exact, case-sensitive |
| `GET /api/education/content` | `level` | `beginner`, `intermediate`, `advanced` | Exact |
| `GET /api/education/content` | `type` | `class`, `challenge` | Exact |
| `GET /api/market/quote` | `symbols` | comma-separated symbols | Unknown symbols skipped |
| `GET /api/market/history` | `range` | `1D`, `1M`, `1Y`, `5Y` (default `1Y`) | Selects the series |

**Sort conventions:**

No sort parameters. Every list has a fixed order.

**Sortable fields per resource:**

| Endpoint | Fixed order |
|---|---|
| Accounts, goals | Display order |
| Transactions | Posted: newest first; scheduled: oldest first |
| Documents | Year descending, action-required first |
| Notifications, market news | Newest first |
| Appointments, advisor messages | Oldest first |
| Quotes, search results | Symbol A–Z |
| Watchlist | Order added |
| Glossary | Term A–Z, language-aware (accents sorted correctly) |
| Education content | Catalogue order |

**Search:**

| Endpoint | Parameter | Matches |
|---|---|---|
| `GET /api/education/glossary` | `q` (≤60) | "contains", case-insensitive, on term or definition in the request language |
| `GET /api/market/search` | `q` (≤40) | "contains", case-insensitive, on symbol or name |

`%` and `_` in `q` are matched literally. Omitting `q` returns everything.

**Combining filter + search + sort:**

Filters combine with paging (`?year=2024&category=Tax&page=1`). Search endpoints have no other filters.

**Common pitfalls:**

- Using enum constant names (`DIVIDEND`) instead of JSON values (`dividend`) → 400 `type: invalid value`.
- Expecting `category` to be validated — an unknown category simply returns no documents.
- Expecting `range` on account performance to filter the series — it does not ([05-endpoint-detail-template.md](05-endpoint-detail-template.md)).
