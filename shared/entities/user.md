# Entity: User

> **Entity ID:** `user`
> **Owning component:** [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#users)); API shape: `TokenResponse (on sign-in)`, `ProfileResponse` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

An online-banking login for an investment client: email, demo credentials, display name, branch account number, date of birth, MFA method and assigned advisor. Neighbouring entities: [`Session`](../../shared/entities/session.md), [`UserProfile`](../../shared/entities/user-profile.md).

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md) |
| Repo | `api-spring` |
| Why this component owns it | Only API path that creates users; sign-in and profile only read them. |
| Storage location (table / collection / index) | `users` in `data/electric-wealth.db` (schema: [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md)) |
| Identifier strategy | `users`: PK `(id)` — Text id — seed persona key (`sarah`) or `u-<8 hex>` for enrolled users |

> The schema is owned by `db-identity-and-auth` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `users.id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#users) | PK; User id (`sarah`, `u-<8 hex>`) |
| `users.email` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#users) | UQ; Login email; `COLLATE NOCASE` |
| `users.password` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#users) | **Demo only, plain text** |
| `users.mfa_code` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#users) | **Demo only**, fixed one-time code |
| `users.name` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#users) | Display name |
| `users.account_number` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#users) | Branch client number (matches `offline_clients`, no FK) |
| `users.date_of_birth` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#users) | `YYYY-MM-DD` |
| `users.created_at` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#users) | Created (UTC) |
| `users.advisor_id` | TEXT | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#users) | FK→`advisors.id` (NO ACTION); Assigned advisor; NULL = none |
| `users.mfa_method` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#users) | `sms` \| `email` |
| `users.terms_accepted_at` | TEXT | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#users) | Terms acceptance time |

**Invariants:**

- `email` unique, case-insensitive (`COLLATE NOCASE`).
- Deletion blocked while `offline_clients.enrolled_user_id` or `registrations.user_id` reference the user.

**Validation hooks:**

- `RegistrationRepository` (email-taken check)
- `users` UNIQUE / CHECK constraints

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Insert user, profile, preferences, answers, snapshot, welcome data | [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md) | `POST /api/registration/complete` | User exists, MFA method set, terms accepted |
| Updated | — | — | No API operation updates this entity | — |
| Archived / Deleted | Hard delete via `ON DELETE CASCADE` from `users` | [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md) | User row deleted (no API operation does this) | Row removed |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`api-advisor`](../../components/electric-wealth-api-spring/api-advisor.md) | `api-spring` | `direct-read` | Reads or writes the same tables through its own repository SQL (shared SQLite file, no service boundary) |
| [`api-auth-sessions`](../../components/electric-wealth-api-spring/api-auth-sessions.md) | `api-spring` | `direct-read` | Reads or writes the same tables through its own repository SQL (shared SQLite file, no service boundary) |
| [`api-profile-and-preferences`](../../components/electric-wealth-api-spring/api-profile-and-preferences.md) | `api-spring` | `direct-read` | Reads or writes the same tables through its own repository SQL (shared SQLite file, no service boundary) |

**Cross-references:** components with `User` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
