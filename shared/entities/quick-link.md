# Entity: QuickLink

> **Entity ID:** `quick-link`
> **Owning component:** [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-goals-and-dashboard`](../../components/electric-wealth-db/db-goals-and-dashboard.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#quick_links)); API shape: `QuickLink` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

An ordered dashboard shortcut (label + mobile deep link). Neighbouring entities: [`Alert`](../../shared/entities/alert.md), `—`.

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md) |
| Repo | `api-spring` |
| Why this component owns it | Reads and localises quick links; registration seeds them. |
| Storage location (table / collection / index) | `quick_links` in `data/electric-wealth.db` (schema: [`db-goals-and-dashboard`](../../components/electric-wealth-db/db-goals-and-dashboard.md)) |
| Identifier strategy | `quick_links`: PK `(user_id, sort_order)` — `(user_id, sort_order)` |

> The schema is owned by `db-goals-and-dashboard` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `quick_links.user_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#quick_links) | PK1, FK→`users.id` (CASCADE) |
| `quick_links.sort_order` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#quick_links) | PK2; Position |
| `quick_links.label` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#quick_links) | English label (localised by the API) |
| `quick_links.deep_link` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#quick_links) |  |

**Invariants:**

- Labels translated to French by `Labels` when `Accept-Language` starts with `fr`.

**Validation hooks:**

- `Labels`

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Insert three starter links | [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md) | `POST /api/registration/complete` (or seed) | Links exist |
| Updated | — | — | No API operation updates this entity | — |
| Archived / Deleted | Hard delete via `ON DELETE CASCADE` from `users` | [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md) | User row deleted (no API operation does this) | Row removed |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md) | `api-spring` | `direct-read` | Reads or writes the same tables through its own repository SQL (shared SQLite file, no service boundary) |
| [`web-dashboard`](../../components/electric-wealth-web/web-dashboard.md) | `web` | `synchronous-read` | Calls `api-dashboard` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `QuickLink` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
