---
title: "Endpoint Detail"
block: reverse-eng/api/consumer/endpoint-detail-template
part: B2
source_repo: electric-wealth-api-spring
source_version: 0.1.0
source_commit: 8a61cdeafb4537b16221d7aa0e9300010abd3387
extracted: 2026-10-15
---

## 5. Endpoint Detail Template

The template is filled once per endpoint, grouped by tag. To keep 49 entries readable, the fields that are the same
for every endpoint are stated once here; each entry lists only what is specific to it. Schemas named below are in
[openapi.yaml](../openapi.yaml) under `components.schemas`.

**Common to every endpoint:**

| Field | Value |
|---|---|
| Auth | 🔒 = `Authorization: Bearer <MFA-verified access token>`; otherwise none |
| Idempotency | n/a — no `Idempotency-Key` support. Naturally idempotent: all GETs, `PUT` profile/preferences/watchlist, `DELETE` watchlist, `POST …/read`, `read-all`. Not idempotent: creates (accounts, goals, appointments, messages, shares, registration start). |
| Request headers | `Content-Type: application/json` for bodies; optional `Accept-Language` where noted (Lang) |
| Response headers | `X-Trace-Id` on every response |
| Errors | `ErrorEnvelope`; 401 `UNAUTHORIZED` on every 🔒 endpoint; 400 `VALIDATION_ERROR` on any invalid param/body; 429 `RATE_LIMITED`, 500 `INTERNAL_ERROR` anywhere |
| Strings | Text fields in request bodies are trimmed before validation |
| Field presence | Response fields are always present unless marked "optional" (omitted when absent) or "nullable" (present, may be `null`) |

curl pattern (substitute method, path and body):

```bash
curl -sS -X POST "http://localhost:4200/api/wealth/goals" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"name":"Trip","category":"Vacation","targetAmount":500000,"currency":"CAD","targetDate":"2028-06-30"}'
```

---

### Health

#### `GET /health`

- **Purpose:** liveness. **Auth:** none.
- **200:** `{ "status": "ok" }`. Does not check the database.

### Auth

#### `POST /api/auth/token`

- **Purpose:** password sign-in; starts a session that still needs MFA. **Auth:** none.
- **Body `TokenRequest`:** `grantType` (string, required, must be `"password"`), `username` (email, required), `password` (string, required, non-empty).
- **200 `TokenResponse`:** `accessToken`, `refreshToken`, `tokenType` = `"Bearer"`, `expiresIn` = `900`, `mfaRequired` = `true`.
- **Errors:** 401 `INVALID_CREDENTIALS` (unknown email or wrong password).
- **Side effects:** creates a session. **Gotcha:** the token is rejected by protected endpoints until MFA is verified.

#### `POST /api/auth/mfa/verify`

- **Purpose:** complete MFA. **Auth:** Bearer = access token from `/token` (not yet verified).
- **Body `MfaVerifyRequest`:** `challengeId` (string, required, any value), `code` (string, required; demo `123456`), `method` (`sms` \| `email`, required).
- **200 `MfaVerifyResponse`:** `accessToken` (same token), `expiresIn` = `900`.
- **Errors:** 401 `UNAUTHORIZED` (missing/expired/revoked token), 401 `INVALID_MFA` (wrong code).
- **Side effects:** marks the session verified and extends expiry to 900 s from now.

#### `POST /api/auth/refresh`

- **Purpose:** rotate tokens. **Auth:** none.
- **Body `RefreshRequest`:** `refreshToken` (string, required).
- **200 `RefreshResponse`:** `accessToken`, `refreshToken`, `tokenType` = `"Bearer"`, `expiresIn` = `900`.
- **Errors:** 401 `INVALID_REFRESH` (unknown or already used/revoked).
- **Side effects:** revokes the old session. **Gotcha:** reusing an old refresh token fails.

#### `POST /api/auth/logout`

- **Purpose:** revoke the session. **Auth:** optional Bearer.
- **Body `LogoutRequest` (optional):** `refreshToken` (string, optional).
- **204:** no body. Never fails; unknown tokens are ignored.

### Registration (public, in order)

Each step after `start` takes the `registrationId`. A registration expires 30 minutes after `start` and cannot be
reused after `complete`.

#### `POST /api/registration/start`

- **Body `RegistrationStartRequest`:** `email` (email, ≤254), `password` (8–72 chars, must contain a letter and a digit).
- **201 `RegistrationStartResponse`:** `registrationId` (e.g. `reg-1a2b3c4d`), `expiresAt` (ISO timestamp, +30 min).
- **Errors:** 409 `EMAIL_TAKEN` (email already has online access).

#### `POST /api/registration/verify-identity`

- **Body `VerifyIdentityRequest`:** `registrationId`, `accountNumber` (exactly 10 digits), `postalCode` (≤7; spaces and case ignored), `dateOfBirth` (`YYYY-MM-DD`).
- **200 `VerifyIdentityResponse`:** `verified` = `true`, `name` (client name on file).
- **Errors:** 404 `REGISTRATION_NOT_FOUND` (expired/unknown/finished), 422 `IDENTITY_MISMATCH` (no client matches all three), 409 `ALREADY_ENROLLED` (client already online).

#### `GET /api/registration/security-questions` (Lang)

- **200:** array of `SecurityQuestion` `{ id, text }` in the requested language.

#### `POST /api/registration/security-questions`

- **Body `SecurityAnswersRequest`:** `registrationId`, `answers` — exactly 2 of `{ questionId, answer (2–100 chars) }`, different questions.
- **200 `StepSavedResponse`:** `{ "saved": true }`. Resubmitting replaces earlier answers.
- **Errors:** 400 (same question twice, unknown question id), 404 `REGISTRATION_NOT_FOUND`, 409 `STEP_OUT_OF_ORDER` (identity not verified).
- **Gotcha:** answers are matched case- and space-insensitively later (stored normalised).

#### `POST /api/registration/trusted-contact` (optional step)

- **Body `TrustedContactRequest`:** `registrationId`, `name` (1–80), `relationship` (1–40), `phone` (≤30, optional), `email` (email, optional).
- **200 `StepSavedResponse`.** **Errors:** 404 `REGISTRATION_NOT_FOUND`, 409 `STEP_OUT_OF_ORDER`.

#### `POST /api/registration/complete`

- **Body `RegistrationCompleteRequest`:** `registrationId`, `mfaMethod` (`sms` \| `email`), `consents` `{ terms, privacy, electronicDelivery }` — all must be `true`.
- **201 `TokenResponse`:** signed-in session with `mfaRequired` = `false`.
- **Errors:** 404 `REGISTRATION_NOT_FOUND`, 409 `STEP_OUT_OF_ORDER` (identity or security answers missing), 409 `EMAIL_TAKEN` (email taken meanwhile).
- **Side effects:** creates the user (profile copied from the branch record, default preferences, empty portfolio, a welcome notification, starter quick links). The new user signs in later with the chosen email/password and MFA code `123456`.

### Dashboard

#### `GET /api/wealth/dashboard` 🔒 (Lang)

- **200 `DashboardResponse`:**
  - `portfolio` `{ totalValue (cents, sum of visible account balances), currency, dayChange, dayChangePct, asOf }`
  - `allocation[]` `{ assetClass, pct }`
  - `goals[]` `{ id, name, progressPct }` (goals pinned to the dashboard)
  - `alerts[]` `{ id, type (ACTION_REQUIRED|INFO|SUCCESS|WARNING), title, deepLink (optional) }`
  - `quickLinks[]` `{ label (localised), deepLink }`
- **Errors:** 404 `NOT_FOUND` "Dashboard not found." (user has no portfolio snapshot).

### Accounts

`AccountSummary` = `{ id, type (TFSA|RRSP|RESP|RDSP|FHSA|NON_REGISTERED|LIRA|RRIF), displayName, nickname (nullable), balance (cents), currency, hidden, dayChange (optional), dayChangePct (optional), contributionRoom (nullable, cents), taxStatus, rateOfReturn (optional) }`.
`AccountDetail` = `AccountSummary` + `holdings[]` `{ symbol, name, quantity, marketValue (cents), currency, weightPct }`.

#### `GET /api/wealth/accounts` 🔒

- **Query:** `page` (≥1, default 1), `pageSize` (1–100, default 20).
- **200 `AccountsListResponse`:** `{ items: AccountSummary[], page, pageSize, total }`. Hidden accounts are excluded.

#### `POST /api/wealth/accounts` 🔒

- **Body `AccountOpenRequest`:** `type` (`TFSA` \| `RRSP` \| `FHSA` \| `NON_REGISTERED`), `displayName` (1–40), `nickname` (≤40, optional).
- **201 `AccountSummary`:** balance 0, currency `CAD`; TFSA: taxStatus "Tax-free", contributionRoom 700000; FHSA: "Tax-free", 800000; RRSP: "Tax-deferred", null; NON_REGISTERED: "Taxable", null.
- **Errors:** 400 for other account types (opened through an advisor).

#### `GET /api/wealth/accounts/{id}` 🔒

- **Path:** `id` (e.g. `tfsa`). **200 `AccountDetail`** (works for hidden accounts too). **Errors:** 404 "Account not found."

#### `PATCH /api/wealth/accounts/{id}` 🔒

- **Body `AccountPatchRequest`:** `nickname` (string ≤40 or `null` to clear; omit to keep), `hidden` (boolean, optional).
- **200 `AccountDetail`.** **Errors:** 404.

#### `GET /api/wealth/accounts/{id}/transactions` 🔒

- **Query:** `page`, `pageSize`, `type` (optional: `contribution|buy|sell|dividend|fee|withdrawal|transfer|scheduled`).
- **200 `TransactionsResponse`:** `{ items: Transaction[], page, pageSize, total, scheduled: Transaction[] }`; `Transaction` = `{ id, date, description, detail (optional), type, amount (cents), currency }`. `items` = posted, newest first; `scheduled` = all upcoming, oldest first (not paged and not filtered by `type`).
- **Errors:** 404 "Account not found."

#### `GET /api/wealth/accounts/{id}/performance` 🔒

- **Query:** `range` (`1M` \| `1Y` \| `ALL`, default `1Y`).
- **200 `PerformanceResponse`:** `{ range, timeWeightedReturnPct, currency, series: [{t: "YYYY-MM", v}], benchmark: { name, series } }`.
- **Errors:** 404 "Performance data not found."
- **Gotcha:** `range` is echoed back but the series is the same for every range.

### Goals

`Goal` = `{ id, name, category (Education|Vacation|Home|Retirement|Safety), currentAmount, targetAmount (cents), currency, targetDate (YYYY-MM-DD), progressPct (0–100) }`.

#### `GET /api/wealth/goals` 🔒

- **Query:** `page`, `pageSize`. **200 `GoalsListResponse`** `{ items: Goal[], page, pageSize, total }`.

#### `POST /api/wealth/goals` 🔒

- **Body `GoalCreateRequest`:** `name` (1–60), `category`, `targetAmount` (>0, ≤10 000 000 000 cents), `currentAmount` (≥0, optional, default 0), `currency` (`"CAD"`), `targetDate` (`YYYY-MM-DD`, after today UTC).
- **201 `Goal`** (`progressPct` = rounded current/target × 100).
- **Errors:** 400 `targetDate: Must be a future date`, `currentAmount: currentAmount cannot exceed targetAmount`.

### Documents

`Document` = `{ id, name, category, year, accountId (nullable), accountLabel (optional), sizeBytes, actionRequired, url }`.

#### `GET /api/wealth/documents` 🔒

- **Query:** `page`, `pageSize`, `year` (integer, optional), `category` (exact match, optional, e.g. `Tax`).
- **200 `DocumentsListResponse`** — newest year first, action-required first.

#### `GET /api/wealth/documents/{id}` 🔒

- **200 `DocumentDetailResponse`:** `{ id, name, url, contentType: "application/pdf", base64Stub }` (a generated one-page PDF). **Errors:** 404 "Document not found."

#### `POST /api/wealth/documents/{id}/share` 🔒

- **Body `DocumentShareRequest`:** `recipient` (email). **200 `DocumentShareResponse`:** `{ recipient, expiresAt }` (now + 7 days).
- **Errors:** 404. **Side effect:** records the share; no email is actually sent.

### Profile and preferences

#### `GET /api/profile` 🔒

- **200 `ProfileResponse`:** `{ name, email, phoneMasked, dateOfBirth, sinMasked (optional), memberSince, address: { line1, line2 (optional), city, province, postalCode, country }, trustedContact (optional) { name, relationship } }`. **Errors:** 404.

#### `PUT /api/profile/address` 🔒

- **Body `ProfileAddressUpdate`:** `{ address: { line1 (1–100), line2 (≤100, optional; blank clears), city (1–60), province (AB|BC|MB|NB|NL|NS|NT|NU|ON|PE|QC|SK|YT), postalCode (Canadian, space/case free), country ("Canada") } }`.
- **200 `ProfileResponse`** with the postal code stored as `A1A 1A1`. **Errors:** 404.

#### `GET /api/preferences` 🔒

- **200 `PreferencesResponse`:** `{ notifications: { email, sms, push }, documentDelivery (digital|paper_and_digital), language (en-CA|fr-CA), theme (light|dark|system), defaultMeetingType (video|phone|in_person) }`. **Errors:** 404.

#### `PUT /api/preferences` 🔒

- **Body `PreferencesUpdate`:** any subset of the fields above; `notifications` flags may be sent individually.
- **200 `PreferencesResponse`** (merged). **Errors:** 404.

### Notifications

#### `GET /api/notifications` 🔒

- **200:** array of `Notification` `{ id, title, body, type, createdAt, read }`, newest first. Not paged.

#### `POST /api/notifications/{id}/read` 🔒

- **200 `NotificationReadResponse`:** `{ id, read: true }`. **Errors:** 404 "Notification not found."

#### `POST /api/notifications/read-all` 🔒

- **200 `NotificationsReadAllResponse`:** `{ updated }` = how many were unread.

### Education

#### `GET /api/education/content` 🔒 (Lang)

- **Query:** `level` (`beginner|intermediate|advanced`, optional), `type` (`class|challenge`, optional).
- **200:** array of `EducationContent` `{ id, title (localised), level, type, durationMin, progressPct }`.

#### `GET /api/education/glossary` 🔒 (Lang)

- **Query:** `q` (≤60 chars, optional; case-insensitive "contains" on term or definition).
- **200:** array of `GlossaryEntry` `{ term, definition }`, sorted A–Z for the language.

#### `POST /api/education/progress` 🔒

- **Body `EducationProgressRequest`:** `contentId`, `progressPct` (0–100).
- **200 `EducationProgressResponse`** `{ contentId, progressPct }` (creates or replaces). **Errors:** 404 "Content not found."

### Advisor

`Appointment` = `{ id, advisorId, meetingType (video|phone|in_person), date, time (HH:MM), status (scheduled|completed|cancelled), notes (optional) }`.

#### `GET /api/advisor/availability` 🔒

- **Query:** `meetingType` (optional; no effect).
- **200 `AvailabilityResponse`:** `{ advisor: { id, name, designation (optional), available }, meetingTypes: [...], days: [{ date, slots: ["09:00", …] }] }` — next 5 weekdays starting tomorrow (Toronto time); slots from `09:00, 10:30, 12:00, 14:30, 16:00` minus booked ones; empty if the advisor is unavailable.
- **Errors:** 404 `NO_ADVISOR`.

#### `GET /api/advisor/appointments` 🔒

- **200:** array of `Appointment`, past and upcoming, oldest first.

#### `POST /api/advisor/appointments` 🔒

- **Body `AppointmentCreateRequest`:** `meetingType`, `date` (`YYYY-MM-DD`), `time` (`HH:MM`), `notes` (≤500, optional).
- **201 `Appointment`** (status `scheduled`).
- **Errors:** 404 `NO_ADVISOR`; 409 `SLOT_UNAVAILABLE` (slot not offered or just taken — re-read availability).

#### `PATCH /api/advisor/appointments/{id}` 🔒

- **Body `AppointmentPatchRequest`:** either `{ date, time }` (reschedule, both required) **or** `{ status: "cancelled" }` — not both, not neither.
- **200 `Appointment`.**
- **Errors:** 400 (`date and time must be changed together`, `Nothing to change`, `Reschedule or cancel, not both`); 404 "Appointment not found." / `NO_ADVISOR`; 409 `NOT_SCHEDULED` (already completed/cancelled); 409 `SLOT_UNAVAILABLE`.

#### `GET /api/advisor/messages` 🔒

- **200:** array of `AdvisorMessage` `{ id, from (advisor|client), text, at }`, oldest first.

#### `POST /api/advisor/messages` 🔒

- **Body `AdvisorMessageCreateRequest`:** `text` (1–2000 after trimming).
- **201 `AdvisorMessage`** (`from` = `client`). **Errors:** 404 `NO_ADVISOR`.

### Market

`Quote` = `{ symbol, name, exchange, price (cents), currency, change (cents), changePct, held, watched }` —
`held`: in one of your visible accounts; `watched`: on your watchlist. Symbols in paths/queries are trimmed and
upper-cased and must match `^[A-Z0-9.]{1,12}$` (else 400 `symbol: Not a valid symbol`).

#### `GET /api/market/quote` 🔒

- **Query:** `symbols` (comma-separated, optional, e.g. `XEQT.TO,AAPL`; omit for all). Unknown symbols are skipped.
- **200:** array of `Quote`, sorted by symbol.

#### `GET /api/market/securities/{symbol}` 🔒

- **200 `SecurityDetail`:** `Quote` fields + `about` (optional). **Errors:** 404 "Unknown symbol X."

#### `GET /api/market/history` 🔒

- **Query:** `symbol` (required), `range` (`1D` \| `1M` \| `1Y` \| `5Y`, default `1Y`).
- **200 `HistoryResponse`:** `{ symbol, range, currency, series: [{ t, c (cents) }] }`; `t` is `HH:MM` for 1D, `YYYY-MM-DD` for 1M, `YYYY-MM` for 1Y/5Y.
- **Errors:** 400 (missing `symbol`: `symbol: required`), 404 unknown symbol.

#### `GET /api/market/search` 🔒

- **Query:** `q` (≤40, optional; contains on symbol or name; omit for all). **200 `SearchResponse`** `{ items: [{ symbol, name, exchange, currency }] }`.

#### `GET /api/market/news` 🔒

- **200:** array of `NewsItem` `{ id, title, source, publishedAt, tag (optional) }`, newest first.

#### `GET /api/market/roundup` 🔒

- **200 `RoundupResponse`:** `{ weekOf, items: [{ id, title, summary, publishedAt }] }` — latest week; `weekOf` = `""` if none.

#### `GET /api/market/baskets` 🔒

- **200:** array of `Basket` `{ id, name, description, return1YPct, holdings, risk (Low|Medium|High) }`.

#### `GET /api/market/watchlist` 🔒

- **200:** array of `Quote` in the order added.

#### `PUT /api/market/watchlist/{symbol}` 🔒

- **200:** the updated watchlist (`Quote[]`). Adding twice is a no-op. **Errors:** 404 unknown symbol.

#### `DELETE /api/market/watchlist/{symbol}` 🔒

- **200:** the updated watchlist. Removing a symbol that is not on the list returns 200 with the unchanged list.
- **Gotcha:** the spec lists 404, but an unknown (well-formed) symbol is not rejected.
