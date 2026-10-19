# Entity: Alert

> **Entity ID:** `alert`
> **Owning component:** [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-goals-and-dashboard`](../../components/electric-wealth-db/db-goals-and-dashboard.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#alerts)); API shape: `DashboardAlert`, `AlertType` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

An action-required or informational dashboard alert carrying a mobile deep link. Neighbouring entities: [`QuickLink`](../../shared/entities/quick-link.md), [`Document`](../../shared/entities/document.md).

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md) |
| Repo | `api-spring` |
| Why this component owns it | Only reader. |
| Storage location (table / collection / index) | `alerts` in `data/electric-wealth.db` (schema: [`db-goals-and-dashboard`](../../components/electric-wealth-db/db-goals-and-dashboard.md)) |
| Identifier strategy | `alerts`: PK `(user_id, id)` — Ids unique per user |

> The schema is owned by `db-goals-and-dashboard` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `alerts.user_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#alerts) | PK1, FK→`users.id` (CASCADE) |
| `alerts.id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#alerts) | PK2 |
| `alerts.type` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#alerts) | `ACTION_REQUIRED` \| `INFO` \| `SUCCESS` \| `WARNING` |
| `alerts.title` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#alerts) |  |
| `alerts.deep_link` | TEXT | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#alerts) | `electricwealth://…` |

**Invariants:**

- `deep_link` uses the mobile scheme `electricwealth://`.

**Validation hooks:**

- `alerts` CHECK on type

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Seeded by `db:seed` | [`db-migration-and-seed-tooling`](../../components/electric-wealth-db/db-migration-and-seed-tooling.md) | `npm run db:seed` / `db:reset` | Row present |
| Updated | — | — | No API operation updates this entity | — |
| Archived / Deleted | Hard delete via `ON DELETE CASCADE` from `users` | [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md) | User row deleted (no API operation does this) | Row removed |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`web-dashboard`](../../components/electric-wealth-web/web-dashboard.md) | `web` | `synchronous-read` | Calls `api-dashboard` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `Alert` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
