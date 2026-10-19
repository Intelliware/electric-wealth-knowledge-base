# Entity: Notification

> **Entity ID:** `notification`
> **Owning component:** [`api-notifications`](../../components/electric-wealth-api-spring/api-notifications.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-notifications`](../../components/electric-wealth-db/db-notifications.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#notifications)); API shape: `Notification`, `NotificationType`, `NotificationReadResponse`, `NotificationsReadAllResponse` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

An in-app message to the user with type (`INFO`, `SUCCESS`, `WARNING`, `ACTION_REQUIRED`), title, body and read flag. Neighbouring entities: [`Alert`](../../shared/entities/alert.md), `—`.

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-notifications`](../../components/electric-wealth-api-spring/api-notifications.md) |
| Repo | `api-spring` |
| Why this component owns it | Owns read-state changes and listing. |
| Storage location (table / collection / index) | `notifications` in `data/electric-wealth.db` (schema: [`db-notifications`](../../components/electric-wealth-db/db-notifications.md)) |
| Identifier strategy | `notifications`: PK `(user_id, id)` — Composite `(user_id, id)` |

> The schema is owned by `db-notifications` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `notifications.user_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#notifications) | PK1, FK→`users.id` (CASCADE) |
| `notifications.id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#notifications) | PK2 |
| `notifications.title` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#notifications) |  |
| `notifications.body` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#notifications) |  |
| `notifications.type` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#notifications) | `ACTION_REQUIRED` \| `INFO` \| `SUCCESS` \| `WARNING` |
| `notifications.created_at` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#notifications) | UTC |
| `notifications.read` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#notifications) | 0/1 |

**Invariants:**

- No deep-link field.

**Validation hooks:**

- `NotificationRepository`

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Insert localised welcome notification (or seed) | [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md) | `POST /api/registration/complete` | Unread |
| Updated | Set `read = 1` | [`api-notifications`](../../components/electric-wealth-api-spring/api-notifications.md) | `POST /api/notifications/{id}/read`, `/read-all` | Read |
| Archived / Deleted | Hard delete via `ON DELETE CASCADE` from `users` | [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md) | User row deleted (no API operation does this) | Row removed |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md) | `api-spring` | `direct-read` | Reads or writes the same tables through its own repository SQL (shared SQLite file, no service boundary) |
| [`web-notifications`](../../components/electric-wealth-web/web-notifications.md) | `web` | `synchronous-read` | Calls `api-notifications` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `Notification` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
