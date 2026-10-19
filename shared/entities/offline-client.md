# Entity: OfflineClient

> **Entity ID:** `offline-client`
> **Owning component:** [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-online-enrolment`](../../components/electric-wealth-db/db-online-enrolment.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#offline_clients)); API shape: `VerifyIdentityResponse` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

A branch (offline) client record used to verify identity during enrolment: account number, postal code, date of birth, name, contact details, language and advisor. Neighbouring entities: [`Registration`](../../shared/entities/registration.md), [`User`](../../shared/entities/user.md).

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md) |
| Repo | `api-spring` |
| Why this component owns it | Only writer. |
| Storage location (table / collection / index) | `offline_clients` in `data/electric-wealth.db` (schema: [`db-online-enrolment`](../../components/electric-wealth-db/db-online-enrolment.md)) |
| Identifier strategy | `offline_clients`: PK `(account_number)` — PK 10-digit `account_number` |

> The schema is owned by `db-online-enrolment` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `offline_clients.account_number` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#offline_clients) | PK; Branch client number |
| `offline_clients.name` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#offline_clients) |  |
| `offline_clients.date_of_birth` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#offline_clients) | `YYYY-MM-DD` (identity factor) |
| `offline_clients.phone_masked` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#offline_clients) |  |
| `offline_clients.sin_masked` | TEXT | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#offline_clients) |  |
| `offline_clients.client_since` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#offline_clients) | Year |
| `offline_clients.address_line1` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#offline_clients) |  |
| `offline_clients.address_line2` | TEXT | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#offline_clients) |  |
| `offline_clients.city` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#offline_clients) |  |
| `offline_clients.province` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#offline_clients) |  |
| `offline_clients.postal_code` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#offline_clients) | `A1A 1A1` (identity factor) |
| `offline_clients.country` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#offline_clients) |  |
| `offline_clients.language` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#offline_clients) | `en-CA` \| `fr-CA` |
| `offline_clients.advisor_id` | TEXT | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#offline_clients) | FK→`advisors.id` (NO ACTION) |
| `offline_clients.enrolled_user_id` | TEXT | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#offline_clients) | FK→`users.id` (NO ACTION); Set when enrolled online |

**Invariants:**

- A client can enrol only once (`ALREADY_ENROLLED`).

**Validation hooks:**

- `RegistrationRepository` identity match

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Seeded by `db:seed` | [`db-migration-and-seed-tooling`](../../components/electric-wealth-db/db-migration-and-seed-tooling.md) | `npm run db:seed` / `db:reset` | Row present |
| Updated | Set `enrolled_user_id` | [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md) | `POST /api/registration/complete` | Enrolled |
| Archived / Deleted | Static reference data; no API operation deletes it | — | — | — |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| _none besides the owner_ | | | |

**Cross-references:** components with `OfflineClient` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
