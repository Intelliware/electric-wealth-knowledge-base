---
title: "Data Integration"
block: reverse-eng/web-app/consumer/data-integration
part: B
source_repo: electric-wealth-web
source_version: 0.1.0
source_commit: bbb4907dd0d866223742c529ea65a75ec8af576f
extracted: 2026-10-16
---

## 4. Data Integration

### 4.1 Inbound Data Channels

| Channel | Supported | Detail |
|---|---|---|
| URL path and query | ✅ | Account id, security symbol, and `?highlight=<documentId>` on `/documents` ([01.3](01-embedding-options.md#13-quick-start-per-mode)) |
| Deep links in API content | ✅ | `electricwealth://…` links in dashboard alerts and quick links, mapped to web URLs |
| Electric Wealth API | ✅ | All displayed data (see [4.5](#45-public-rest-api-surface)) |
| `postMessage`, SDK calls, prefill parameters | ❌ | — |

### 4.2 Outbound Data Channels

| Channel | Supported | Detail |
|---|---|---|
| Writes to the Electric Wealth API | ✅ | User actions (preferences, address, account opening, goals, bookings, messages, watchlist, education progress, document sharing, notification read state) |
| Document download | ✅ | User-initiated PDF download of one document at a time |
| Document sharing | ✅ | Via the API (`POST /api/wealth/documents/{id}/share`): sends a time-limited link to an email recipient |
| Analytics, telemetry, error reporting, third-party calls | ❌ | The app calls nothing but its own origin's `/api` |
| Events to a host | ❌ | See [06](06-event-and-callback-api.md) |

### 4.3 postMessage Protocol (iframe)

Not supported. The app neither sends nor listens for `message` events.

### 4.4 Seeding / Prefill

Not supported. No query parameter pre-fills a form. (The login email field is pre-filled with a demo address in the
source; this is demo behaviour, not an integration feature.)

### 4.5 Public REST API Surface

The web app exposes **no API of its own**. Everything it shows comes from the **Electric Wealth API**
(`electric-wealth-api-spring`), whose consumer guide is the integration point for data:
`source-docs/electric-wealth-api-spring/api-consumer/` and `openapi.yaml`.

The web app uses 47 of the API's 49 operations (not `GET /health` or `GET /api/market/quote`):

| Area | Operations used |
|---|---|
| Auth | token, MFA verify, refresh, logout |
| Registration | start, verify identity, security questions (get, save), trusted contact, complete |
| Dashboard, profile, preferences | dashboard; profile get, address update; preferences get/update |
| Accounts | list, open, get, rename, transactions, performance |
| Documents | list, get (download), share |
| Goals | list, create |
| Notifications | list, mark read, mark all read |
| Education | content, progress, glossary |
| Advisor | availability, appointments (list, book, reschedule/cancel), messages (list, send) |
| Market | watchlist (get, add, remove), security, history, search, news, roundup, baskets |

Its TypeScript client is generated from the API's committed `openapi.json`; a stale copy fails the web app's
`npm run verify`.

### 4.6 Webhooks (Server → Host)

Not supported. The web app has no server component. (The API emits no webhooks either.)

### 4.7 Exports and Bulk Data

No bulk export. Users can download individual documents (currently a stub PDF returned by the API). There is no CSV
or statement export of accounts, transactions or holdings in the web app; use the API for programmatic access.

### 4.8 Data-Integration Anti-Patterns

- Do not scrape the web app's pages or depend on its HTML/CSS class names; they carry no stability promise.
- Do not reuse a user's web session tokens from another product: they live in that tab's `sessionStorage` and are
  rotated every 10 minutes.
- Do not invent new deep-link sections and expect the web app to handle them; only the sections in
  [01.3](01-embedding-options.md#13-quick-start-per-mode) are mapped.

