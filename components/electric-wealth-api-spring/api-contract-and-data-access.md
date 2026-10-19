---
component_id: api-contract-and-data-access
repo: api-spring
type: framework
description: >-
  Use when a change touches the committed openapi.json contract, OpenAPI generation conventions, shared DTO/enum types, JSON serialisation settings, application configuration, or the JdbcClient row-mapping helpers used by every repository.
triggers:
  - "openapi.json"
  - "OpenAPI contract"
  - "contract parity"
  - "springdoc"
  - "ApiEnums"
  - "Jackson config"
  - "DatabaseConfig"
  - "JdbcClient"
  - "Rows mapper"
  - "pagination PageResult"
  - "application.yml"
paths:
  - "openapi.json"
  - "pom.xml"
  - "src/main/resources/application.yml"
  - "src/main/java/com/electricwealth/api/ElectricWealthApplication.java"
  - "src/main/java/com/electricwealth/api/config/**"
  - "src/main/java/com/electricwealth/api/dto/ApiEnums.java"
  - "src/main/java/com/electricwealth/api/dto/Text.java"
  - "src/main/java/com/electricwealth/api/repository/Rows.java"
  - "src/main/java/com/electricwealth/api/repository/SqlValues.java"
  - "src/main/java/com/electricwealth/api/repository/PageResult.java"
  - "src/main/java/com/electricwealth/api/web/ApiParams.java"
  - "src/test/java/com/electricwealth/api/OpenApiSpecTest.java"
  - "src/test/java/com/electricwealth/api/ContractParityTest.java"
  - "src/test/java/com/electricwealth/api/ApiTestBase.java"
depends_on: []
consumed_by:
  - "api-accounts"
  - "api-advisor"
  - "api-auth-sessions"
  - "api-dashboard"
  - "api-documents"
  - "api-education"
  - "api-goals"
  - "api-health"
  - "api-market"
  - "api-notifications"
  - "api-profile-and-preferences"
  - "api-registration"
  - "api-request-pipeline"
  - "web-api-client"
data_entities: []
last_verified_against_commit: 3f7403e33d97b39872e89690431c7880dea98ae4
last_feature_id: ""
source_doc: docs/api-internal/03-mapping-openapi-contract-internal-implementation.md
verification_signal: exported-symbols-diff
---

# OpenAPI Contract and Data Access Foundation

> **Repo:** `api-spring` ([repo summary](../../repos/electric-wealth-api-spring/README.md)) · **Type:** `framework` · **Source doc:** [`docs/api-internal/03-mapping-openapi-contract-internal-implementation.md`](../../../electric-wealth-api-spring/docs/api-internal/03-mapping-openapi-contract-internal-implementation.md)

## 1. Overview

The spec is generated from code by springdoc and committed as `openapi.json`; `OpenApiSpecTest` fails the build if they differ and `ContractParityTest` compares it against the Node API's spec. `RequiredRecordFieldsConverter` makes every record field required unless `@Nullable`; `ApiErrors` adds error responses; `JacksonConfig` and `SqlValues` keep JSON byte-compatible with the Node API.

Data access is plain SQL via Spring `JdbcClient` (no ORM): `Rows.of(Record.class)` maps snake_case columns to record components, `PageResult` / `SqlValues.offset` implement `{ items, page, pageSize, total }` paging. `DatabaseConfig` opens `../electric-wealth-db/data/electric-wealth.db` (foreign keys on, 3 s busy timeout, Hikari pool of 4) and refuses to start if the file is missing.

**Consumed by:** [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md), [`api-advisor`](../../components/electric-wealth-api-spring/api-advisor.md), [`api-auth-sessions`](../../components/electric-wealth-api-spring/api-auth-sessions.md), [`api-dashboard`](../../components/electric-wealth-api-spring/api-dashboard.md), [`api-documents`](../../components/electric-wealth-api-spring/api-documents.md), [`api-education`](../../components/electric-wealth-api-spring/api-education.md), [`api-goals`](../../components/electric-wealth-api-spring/api-goals.md), [`api-health`](../../components/electric-wealth-api-spring/api-health.md), [`api-market`](../../components/electric-wealth-api-spring/api-market.md), [`api-notifications`](../../components/electric-wealth-api-spring/api-notifications.md), [`api-profile-and-preferences`](../../components/electric-wealth-api-spring/api-profile-and-preferences.md), [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md), [`api-request-pipeline`](../../components/electric-wealth-api-spring/api-request-pipeline.md), [`web-api-client`](../../components/electric-wealth-web/web-api-client.md)

---

## 2. Key Responsibilities

- Generate and guard the OpenAPI contract.
- Provide shared enums, text trimming and parameter annotations.
- Configure JSON serialisation for Node parity.
- Provide row mapping, paging and timestamp helpers.
- Open the SQLite datasource.

**Explicitly NOT in scope:**

- Schema DDL and migrations — owned by [`db-migration-and-seed-tooling`](../../components/electric-wealth-db/db-migration-and-seed-tooling.md)
- Per-feature DTOs and SQL — owned by [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Exported symbols / shared behaviour | Every dependant: `api-accounts`, `api-advisor`, `api-auth-sessions`, `api-dashboard`, `api-documents`, `api-education`, `api-goals`, `api-health`, `api-market`, `api-notifications`, `api-profile-and-preferences`, `api-registration`, `api-request-pipeline`, `web-api-client` | `exported-symbols-diff`, then the repo's full gate |
| Internal refactor (no interface change) | This component only | Repo gate (`./mvnw test` or `npm run verify`) |
| Data-entity schema change | Indirect — via the features that use this foundation | Repo gate |
| Dependency upgrade | Whole repo | `./mvnw verify` or `npm run build` |

**Upstream dependents to notify:** `api-accounts`, `api-advisor`, `api-auth-sessions`, `api-dashboard`, `api-documents`, `api-education`, `api-goals`, `api-health`, `api-market`, `api-notifications`, `api-profile-and-preferences`, `api-registration`, `api-request-pipeline`, `web-api-client`

**Downstream services to monitor post-change:** None.

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (2026-10-19)

**Open follow-ups from extraction:**

- `ContractParityTest` is skipped when the Node API repo (`electric-wealth-api`) is not cloned alongside.
