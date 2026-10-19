# Entity: Advisor

> **Entity ID:** `advisor`
> **Owning component:** [`api-advisor`](../../components/electric-wealth-api-spring/api-advisor.md)
> **Owning repo:** `api-spring`
> **Source of truth:** schema in [`db-advisor`](../../components/electric-wealth-db/db-advisor.md) ([data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#advisors)); API shape: `AdvisorInfo` in [`openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)
> **Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (`api-spring`), schema `e6262fcddca7f4c0488248ca8881483d9b920f0a` (`db`)

---

## 1. Definition

A financial advisor (name, designation, availability) assigned to users and branch clients. Neighbouring entities: [`Appointment`](../../shared/entities/appointment.md), [`AdvisorMessage`](../../shared/entities/advisor-message.md).

---

## 2. Owning Component

| Field | Value |
|---|---|
| `component_id` | [`api-advisor`](../../components/electric-wealth-api-spring/api-advisor.md) |
| Repo | `api-spring` |
| Why this component owns it | Only reader; users reference it through `users.advisor_id`. |
| Storage location (table / collection / index) | `advisors` in `data/electric-wealth.db` (schema: [`db-advisor`](../../components/electric-wealth-db/db-advisor.md)) |
| Identifier strategy | `advisors`: PK `(id)` — Text id |

> The schema is owned by `db-advisor` in the `db` repo. The Spring API
> and the Node API share the same SQLite file, so "owning component" here means the owner of the business rules.
> `direct-read` rows in § 5 are other API components that query this storage through their own SQL.

---

## 3. Fields / Schema

| Field | Type | Required | Source of Truth | Notes |
|---|---|---|---|---|
| `advisors.id` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#advisors) | PK; `adv-1` |
| `advisors.name` | TEXT | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#advisors) |  |
| `advisors.designation` | TEXT | no | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#advisors) | e.g. `CFP` |
| `advisors.available` | INTEGER | yes | [data dictionary](../../source-docs/electric-wealth-db/data-dictionary.md#advisors) | 0/1; 0 = no bookable slots |

**Invariants:**

- An advisor with `available = 0` has no slots.

**Validation hooks:**

- `AdvisorRepository`

---

## 4. Lifecycle

| Stage | Action | Performed By | Trigger | Resulting State |
|---|---|---|---|---|
| Created | Seeded by `db:seed` | [`db-migration-and-seed-tooling`](../../components/electric-wealth-db/db-migration-and-seed-tooling.md) | `npm run db:seed` / `db:reset` | Row present |
| Updated | — | — | No API operation updates this entity | — |
| Archived / Deleted | Cannot be deleted while referenced (NO ACTION FKs) | — | — | — |
| Restored (if applicable) | — | — | — | — |

**State transitions:**

_No non-trivial state machine — `created → updated* → deleted` covers it._

---

## 5. Used By

| Component ID | Repo | Access Mode | Why It Reads This Entity |
|---|---|---|---|
| [`web-advisor`](../../components/electric-wealth-web/web-advisor.md) | `web` | `synchronous-read` | Calls `api-advisor` over HTTP (via `web-api-client`) to render it |

**Cross-references:** components with `Advisor` in `data_entities` — see the Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).
