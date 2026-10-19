# Entity: UserPreferences

> **Entity ID:** `user-preferences`
> **Owning component:** [`api-profile-and-preferences`](../../components/electric-wealth-api-spring/api-profile-and-preferences.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_preferences)); API shape: `PreferencesResponse`, `PreferencesUpdate`, `NotificationPrefs` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

Per-user settings: notification channels (email, SMS, push), document delivery, language, theme and default advisor meeting type. Neighbouring entities: [`UserProfile`](../../shared/entities/user-profile.md), [`Appointment`](../../shared/entities/appointment.md).

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-profile-and-preferences`](../../components/electric-wealth-api-spring/api-profile-and-preferences.md) |
| Repo | `api-spring` |
| Why this component owns it | Only component that updates preferences. |
| Storage location (table / collection / index) | `user_preferences` in `data/electric-wealth.db` (schema: [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md)) |
| Identifier strategy | `user_preferences`: PK `(user_id)` — PK = `user_id` (1:0..1 with `users`) |

> The schema is owned by `db-identity-and-auth` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `user_preferences.user_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_preferences) | PK, FK→`users.id` (CASCADE); Owner |
| `user_preferences.notify_email` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_preferences) | 0/1 |
| `user_preferences.notify_sms` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_preferences) | 0/1 |
| `user_preferences.notify_push` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_preferences) | 0/1 |
| `user_preferences.document_delivery` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_preferences) | `digital` \| `paper_and_digital` |
| `user_preferences.language` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_preferences) | `en-CA` \| `fr-CA` |
| `user_preferences.theme` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_preferences) | `light` \| `dark` \| `system` |
| `user_preferences.default_meeting_type` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_preferences) | `video` \| `phone` \| `in_person` |

**Invariants:**

- No column has a DB default — inserts must supply every column.
- Enumerations enforced by CHECK constraints.

**Validation hooks:**

- `ProfileRepository` merge logic
- `user_preferences` CHECK constraints

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Insert defaults (email on, SMS per MFA method, push off, digital, branch language, theme `system`, meeting `video`) | [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md) | `POST /api/registration/complete` | Preferences exist |
| Updated | Partial merge update | [`api-profile-and-preferences`](../../components/electric-wealth-api-spring/api-profile-and-preferences.md) | `PUT /api/preferences` | Merged preferences |
| Archived / Deleted | Hard delete via `ON DELETE CASCADE` from `users` | [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md) | User row deleted (no API operation does this) | Row removed |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md) | `api-spring` | `direct-read` | Reads or writes the same tables through its own repository SQL (shared SQLite file, no service boundary) |
| [`web-advisor`](../../components/electric-wealth-web/web-advisor.md) | `web` | `synchronous-read` | Calls `api-profile-and-preferences` over HTTP (via `web-api-client`) to render it |
| [`web-app-shell`](../../components/electric-wealth-web/web-app-shell.md) | `web` | `synchronous-read` | Calls `api-profile-and-preferences` over HTTP (via `web-api-client`) to render it |
| [`web-settings`](../../components/electric-wealth-web/web-settings.md) | `web` | `synchronous-read` | Calls `api-profile-and-preferences` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `UserPreferences` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
