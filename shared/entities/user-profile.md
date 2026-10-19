# Entity: UserProfile

> **Entity ID:** `user-profile`
> **Owning component:** [`api-profile-and-preferences`](../../components/electric-wealth-api-spring/api-profile-and-preferences.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_profiles)); API shape: `ProfileResponse`, `Address`, `AddressInput`, `TrustedContact` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

Personal details shown to the client: masked phone and SIN, member-since year, mailing address and trusted contact. Neighbouring entities: [`User`](../../shared/entities/user.md), [`UserPreferences`](../../shared/entities/user-preferences.md).

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-profile-and-preferences`](../../components/electric-wealth-api-spring/api-profile-and-preferences.md) |
| Repo | `api-spring` |
| Why this component owns it | Only component that updates the profile. |
| Storage location (table / collection / index) | `user_profiles` in `data/electric-wealth.db` (schema: [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md)) |
| Identifier strategy | `user_profiles`: PK `(user_id)` — PK = `user_id` (1:0..1 with `users`) |

> The schema is owned by `db-identity-and-auth` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `user_profiles.user_id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_profiles) | PK, FK→`users.id` (CASCADE); Owner |
| `user_profiles.phone_masked` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_profiles) | Masked phone (`+1 (416) •••-••42`) |
| `user_profiles.sin_masked` | TEXT | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_profiles) | Masked SIN (`••• ••• 821`) |
| `user_profiles.member_since` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_profiles) | Year joined |
| `user_profiles.address_line1` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_profiles) |  |
| `user_profiles.address_line2` | TEXT | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_profiles) |  |
| `user_profiles.city` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_profiles) |  |
| `user_profiles.province` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_profiles) | e.g. `ON` |
| `user_profiles.postal_code` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_profiles) | e.g. `M5V 2A1` |
| `user_profiles.country` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_profiles) | e.g. `Canada` |
| `user_profiles.trusted_contact_name` | TEXT | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_profiles) |  |
| `user_profiles.trusted_contact_relationship` | TEXT | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#user_profiles) |  |

**Invariants:**

- Country must be `Canada`; postal code stored as `A1A 1A1`; blank `line2` stored as NULL.

**Validation hooks:**

- `ProfileDtos.AddressInput` validation
- `ProfileRepository`

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Insert copied from the branch record | [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md) | `POST /api/registration/complete` | Profile exists |
| Updated | Replace mailing address | [`api-profile-and-preferences`](../../components/electric-wealth-api-spring/api-profile-and-preferences.md) | `PUT /api/profile/address` | Address normalised (`A1A 1A1`, country `Canada`) |
| Archived / Deleted | Hard delete via `ON DELETE CASCADE` from `users` | [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md) | User row deleted (no API operation does this) | Row removed |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md) | `api-spring` | `direct-read` | Reads or writes the same tables through its own repository SQL (shared SQLite file, no service boundary) |
| [`web-dashboard`](../../components/electric-wealth-web/web-dashboard.md) | `web` | `synchronous-read` | Calls `api-profile-and-preferences` over HTTP (via `web-api-client`) to render it |
| [`web-profile`](../../components/electric-wealth-web/web-profile.md) | `web` | `synchronous-read` | Calls `api-profile-and-preferences` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `UserProfile` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
