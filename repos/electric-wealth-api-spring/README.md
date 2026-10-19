# Electric Wealth API (Spring) — Repository Summary

> **Repo code:** `api-spring`
> **Role:** api
> **Type:** java-21-spring-boot-4-maven
> **Path in source tree:** `{project-root}/../electric-wealth-api-spring`
> **Primary:** yes
> **Last indexed commit:** 3f7403e33d97b39872e89690431c7880dea98ae4

This page is the per-repository entry point inside the Electric Wealth knowledge base. The
[`ARCHITECTURE.md`](../../ARCHITECTURE.md) Repository Map (§ 2) links here. KB folder slug: `electric-wealth-api-spring`.

---

## 1. Purpose

The REST backend for Electric Wealth: sign-in with password + MFA, online enrolment, dashboard, accounts, goals, documents, profile and preferences, notifications, advisor booking and messaging, education and market data — 49 operations across 12 controllers. It is a port of the Node API (`electric-wealth-api`) and must return identical JSON, enforced by contract tests. It does not own the database schema (it never runs DDL) and makes no outbound calls; without it the web app has no data.

---

## 2. Owner

| Field | Value |
|---|---|
| Owning team | Not recorded in RE output |
| Primary maintainer | Not recorded in RE output |
| Backup maintainer | Not recorded in RE output |
| Contact channel | Not recorded in RE output |
| On-call rotation | None defined (local / workshop project) |

> Ownership is captured by `capture-solution-context` (`shared/solution-context/50-ownership-and-raci.md`), which has not run yet.

---

## 3. Tech Stack

| Layer | Technology / Version | Notes |
|---|---|---|
| Language(s) | Java 21 |  |
| Framework(s) | Spring Boot 4.1.1 (webmvc, validation, security, jdbc); springdoc-openapi 3.1.1 | No ORM, no service layer |
| Build / package manager | Maven wrapper (`./mvnw`) | Single module |
| Test stack | spring-boot-starter-webmvc-test, spring-boot-starter-security-test (JUnit) | 6 feature test classes + `OpenApiSpecTest` + `ContractParityTest` |
| Datastore(s) | SQLite via `org.xerial:sqlite-jdbc` 3.53.4.0, HikariCP pool of 4 | File owned by `electric-wealth-db` |
| Runtime / deploy target | Local JVM, port 4200 | No deployment environment defined |
| Key dependencies (top 5–10) | spring-boot-starter-*, springdoc-openapi-starter-webmvc-ui, sqlite-jdbc |  |

---

## 4. Top Components

Full list: Component Catalog in [`ARCHITECTURE.md`](../../ARCHITECTURE.md#3-component-catalog).

| Component ID | Type | Description (Use-when …) | Doc |
|---|---|---|---|
| [`api-request-pipeline`](../../components/electric-wealth-api-spring/api-request-pipeline.md) | `framework` | Use when a change touches how every API request is authenticated, traced, rate-limited or turned into the standard error envelope — the bearer-token filter, Spring Security config, trace and rate-limit filters, or GlobalExceptionHandler. | [doc](../../components/electric-wealth-api-spring/api-request-pipeline.md) |
| [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md) | `framework` | Use when a change touches the committed openapi.json contract, OpenAPI generation conventions, shared DTO/enum types, JSON serialisation settings, application configuration, or the JdbcClient row-mapping helpers used by every repository. | [doc](../../components/electric-wealth-api-spring/api-contract-and-data-access.md) |
| [`api-auth-sessions`](../../components/electric-wealth-api-spring/api-auth-sessions.md) | `api-feature` | Use when a client signs in with email and password, completes the MFA step, refreshes or revokes a bearer session through the /api/auth endpoints. | [doc](../../components/electric-wealth-api-spring/api-auth-sessions.md) |
| [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md) | `api-feature` | Use when an existing branch client enrols for online access through the multi-step /api/registration flow — login choice, identity match, security questions, optional trusted contact and completion. | [doc](../../components/electric-wealth-api-spring/api-registration.md) |
| [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md) | `api-feature` | Use when a client lists, opens, renames or hides investment accounts, or reads account holdings, transactions or performance through /api/wealth/accounts. | [doc](../../components/electric-wealth-api-spring/api-accounts.md) |
| [`api-profile-and-preferences`](../../components/electric-wealth-api-spring/api-profile-and-preferences.md) | `api-feature` | Use when a client reads their profile, replaces their mailing address, or reads or partially updates preferences (theme, language, notification channels, document delivery, default meeting type). | [doc](../../components/electric-wealth-api-spring/api-profile-and-preferences.md) |
| [`api-advisor`](../../components/electric-wealth-api-spring/api-advisor.md) | `api-feature` | Use when a client checks advisor availability, books, reschedules or cancels an appointment, or reads and sends secure messages through /api/advisor. | [doc](../../components/electric-wealth-api-spring/api-advisor.md) |
| [`api-market`](../../components/electric-wealth-api-spring/api-market.md) | `api-feature` | Use when a client reads quotes, security detail, price history, search results, news, the weekly roundup or thematic baskets, or adds or removes watchlist symbols through /api/market. | [doc](../../components/electric-wealth-api-spring/api-market.md) |

---

## 5. Public Interfaces

| Kind | Identifier | Description | Source of Truth |
|---|---|---|---|
| `http-endpoint` | `GET /health` | Health Endpoint (`api-health`) | `openapi.json` |
| `http-endpoint` | `POST /api/auth/token` | Auth and Sessions (`api-auth-sessions`) | `openapi.json` |
| `http-endpoint` | `POST /api/auth/mfa/verify` | Auth and Sessions (`api-auth-sessions`) | `openapi.json` |
| `http-endpoint` | `POST /api/auth/refresh` | Auth and Sessions (`api-auth-sessions`) | `openapi.json` |
| `http-endpoint` | `POST /api/auth/logout` | Auth and Sessions (`api-auth-sessions`) | `openapi.json` |
| `http-endpoint` | `POST /api/registration/start` | Online Registration (`api-registration`) | `openapi.json` |
| `http-endpoint` | `POST /api/registration/verify-identity` | Online Registration (`api-registration`) | `openapi.json` |
| `http-endpoint` | `GET /api/registration/security-questions` | Online Registration (`api-registration`) | `openapi.json` |
| `http-endpoint` | `POST /api/registration/security-questions` | Online Registration (`api-registration`) | `openapi.json` |
| `http-endpoint` | `POST /api/registration/trusted-contact` | Online Registration (`api-registration`) | `openapi.json` |
| `http-endpoint` | `POST /api/registration/complete` | Online Registration (`api-registration`) | `openapi.json` |
| `http-endpoint` | `GET /api/wealth/dashboard` | Dashboard (`api-dashboard`) | `openapi.json` |
| `http-endpoint` | `GET /api/wealth/accounts` | Accounts (`api-accounts`) | `openapi.json` |
| `http-endpoint` | `POST /api/wealth/accounts` | Accounts (`api-accounts`) | `openapi.json` |
| `http-endpoint` | `GET /api/wealth/accounts/{id}` | Accounts (`api-accounts`) | `openapi.json` |
| `http-endpoint` | `PATCH /api/wealth/accounts/{id}` | Accounts (`api-accounts`) | `openapi.json` |
| `http-endpoint` | `GET /api/wealth/accounts/{id}/transactions` | Accounts (`api-accounts`) | `openapi.json` |
| `http-endpoint` | `GET /api/wealth/accounts/{id}/performance` | Accounts (`api-accounts`) | `openapi.json` |
| `http-endpoint` | `GET /api/wealth/goals` | Savings Goals (`api-goals`) | `openapi.json` |
| `http-endpoint` | `POST /api/wealth/goals` | Savings Goals (`api-goals`) | `openapi.json` |
| `http-endpoint` | `GET /api/wealth/documents` | Documents (`api-documents`) | `openapi.json` |
| `http-endpoint` | `GET /api/wealth/documents/{id}` | Documents (`api-documents`) | `openapi.json` |
| `http-endpoint` | `POST /api/wealth/documents/{id}/share` | Documents (`api-documents`) | `openapi.json` |
| `http-endpoint` | `GET /api/profile` | Profile and Preferences (`api-profile-and-preferences`) | `openapi.json` |
| `http-endpoint` | `PUT /api/profile/address` | Profile and Preferences (`api-profile-and-preferences`) | `openapi.json` |
| `http-endpoint` | `GET /api/preferences` | Profile and Preferences (`api-profile-and-preferences`) | `openapi.json` |
| `http-endpoint` | `PUT /api/preferences` | Profile and Preferences (`api-profile-and-preferences`) | `openapi.json` |
| `http-endpoint` | `GET /api/notifications` | Notifications (`api-notifications`) | `openapi.json` |
| `http-endpoint` | `POST /api/notifications/{id}/read` | Notifications (`api-notifications`) | `openapi.json` |
| `http-endpoint` | `POST /api/notifications/read-all` | Notifications (`api-notifications`) | `openapi.json` |
| `http-endpoint` | `GET /api/advisor/availability` | Advisor Booking and Messaging (`api-advisor`) | `openapi.json` |
| `http-endpoint` | `GET /api/advisor/appointments` | Advisor Booking and Messaging (`api-advisor`) | `openapi.json` |
| `http-endpoint` | `POST /api/advisor/appointments` | Advisor Booking and Messaging (`api-advisor`) | `openapi.json` |
| `http-endpoint` | `PATCH /api/advisor/appointments/{id}` | Advisor Booking and Messaging (`api-advisor`) | `openapi.json` |
| `http-endpoint` | `GET /api/advisor/messages` | Advisor Booking and Messaging (`api-advisor`) | `openapi.json` |
| `http-endpoint` | `POST /api/advisor/messages` | Advisor Booking and Messaging (`api-advisor`) | `openapi.json` |
| `http-endpoint` | `GET /api/education/content` | Education (`api-education`) | `openapi.json` |
| `http-endpoint` | `GET /api/education/glossary` | Education (`api-education`) | `openapi.json` |
| `http-endpoint` | `POST /api/education/progress` | Education (`api-education`) | `openapi.json` |
| `http-endpoint` | `GET /api/market/quote` | Market Data and Watchlist (`api-market`) | `openapi.json` |
| `http-endpoint` | `GET /api/market/securities/{symbol}` | Market Data and Watchlist (`api-market`) | `openapi.json` |
| `http-endpoint` | `GET /api/market/history` | Market Data and Watchlist (`api-market`) | `openapi.json` |
| `http-endpoint` | `GET /api/market/search` | Market Data and Watchlist (`api-market`) | `openapi.json` |
| `http-endpoint` | `GET /api/market/news` | Market Data and Watchlist (`api-market`) | `openapi.json` |
| `http-endpoint` | `GET /api/market/roundup` | Market Data and Watchlist (`api-market`) | `openapi.json` |
| `http-endpoint` | `GET /api/market/baskets` | Market Data and Watchlist (`api-market`) | `openapi.json` |
| `http-endpoint` | `GET /api/market/watchlist` | Market Data and Watchlist (`api-market`) | `openapi.json` |
| `http-endpoint` | `PUT /api/market/watchlist/{symbol}` | Market Data and Watchlist (`api-market`) | `openapi.json` |
| `http-endpoint` | `DELETE /api/market/watchlist/{symbol}` | Market Data and Watchlist (`api-market`) | `openapi.json` |
| `config-knob` | `PORT` | HTTP port (default 4200) | `src/main/resources/application.yml` |
| `config-knob` | `DB_PATH` | SQLite file path | `src/main/resources/application.yml` |
| `config-knob` | `CORS_ORIGINS` | Allowed origins | `src/main/resources/application.yml` |
| `config-knob` | `ew.rate-limit-per-minute` | Per-IP limit (300) | `src/main/resources/application.yml` |

---

## 6. Internal & Consumer Doc Pointers

Part A paths are relative to the source repo's root; Part B lives in this KB under `source-docs/` (KB-relative path shown).

| Document | Audience | Location |
|---|---|---|
| Part A — Internal docs | Maintainers of api-spring | [`docs/api-internal/`](../../../electric-wealth-api-spring/docs/api-internal/01-api-overview.md) (source repo) |
| Part B — Consumer / Implementer docs | Downstream callers | [`source-docs/electric-wealth-api-spring/api-consumer/`](../../source-docs/electric-wealth-api-spring/api-consumer/) (KB) |
| API contract (OpenAPI / AsyncAPI / GraphQL SDL) | Tooling, integration tests | [`openapi.json`](../../../electric-wealth-api-spring/openapi.json) (KB copy: [`source-docs/electric-wealth-api-spring/openapi.yaml`](../../source-docs/electric-wealth-api-spring/openapi.yaml)) |
| Architecture Decision Records (repo-local) | Maintainers, architects | |
| Runbooks / on-call playbooks | On-call engineers | |
| Onboarding guide | New contributors | [`README.md`](../../../electric-wealth-api-spring/README.md) |
| RE verification report | KB maintainers | [`docs/api-internal/verification-report.md`](../../../electric-wealth-api-spring/docs/api-internal/verification-report.md) — `MECHANICAL: GO` |
