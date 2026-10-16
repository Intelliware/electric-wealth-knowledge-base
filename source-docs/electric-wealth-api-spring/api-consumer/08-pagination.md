---
title: "Pagination"
block: reverse-eng/api/consumer/pagination
part: B2
source_repo: electric-wealth-api-spring
source_version: 0.1.0
source_commit: 8a61cdeafb4537b16221d7aa0e9300010abd3387
extracted: 2026-10-15
---

## 8. Pagination

**Pagination model:** Offset (page number + page size). No cursors.

**Default & maximum page size:**

| Parameter | Default | Allowed |
|---|---|---|
| `page` | 1 | ≥ 1 |
| `pageSize` | 20 | 1–100 |

Out-of-range values → 400 `VALIDATION_ERROR` (e.g. `pageSize: must be less than or equal to 100`).

Paged endpoints: `GET /api/wealth/accounts`, `GET /api/wealth/accounts/{id}/transactions`, `GET /api/wealth/goals`,
`GET /api/wealth/documents`.

**Cursor pagination (preferred):**

n/a — not offered.

**End-of-data signal:**

Stop when `page * pageSize >= total`, or when `items` is empty. A page past the end returns `items: []` with the
same `total`.

**Cursor opacity:**

n/a.

**Offset pagination (legacy / specific endpoints):**

```http
GET /api/wealth/documents?page=2&pageSize=10&year=2024
→ { "items": [ …up to 10… ], "page": 2, "pageSize": 10, "total": 23 }
```

The transactions endpoint pages only the posted transactions (`items`); `scheduled` is always the full list.

**Stability under concurrent writes:**

Each page is a fresh query with a fixed order, so inserts/hides between page requests can shift items across pages
(duplicates or gaps). Orders: accounts by display order; transactions newest first; goals by display order;
documents newest year first, action-required first.

**Total count:**

`total` is exact, computed with the same filters on every request.

**Sample pseudocode:**

```ts
let page = 1, all = [];
for (;;) {
  const r = await get(`/api/wealth/goals?page=${page}&pageSize=100`);
  all.push(...r.items);
  if (page * r.pageSize >= r.total || r.items.length === 0) break;
  page++;
}
```

**Common pitfalls:**

- Expecting other lists (notifications, appointments, messages, quotes, …) to be paged — they return everything.
- Hidden accounts are excluded from `GET /api/wealth/accounts` and its `total`.
