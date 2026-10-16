---
title: "Endpoint Catalog"
block: reverse-eng/api/consumer/endpoint-catalog
part: B2
source_repo: electric-wealth-api-spring
source_version: 0.1.0
source_commit: 8a61cdeafb4537b16221d7aa0e9300010abd3387
extracted: 2026-10-15
---

## 4. Endpoint Catalog

**Conventions used in this catalog:**

- 🔒 = requires an MFA-verified Bearer token. Every 🔒 endpoint can also return 401.
- "Paged" = accepts `page` / `pageSize` and returns `{ items, page, pageSize, total }` ([08-pagination.md](08-pagination.md)).
- "Lang" = honours `Accept-Language` (`fr…` → French, otherwise English).
- Every endpoint can return 429 `RATE_LIMITED` and 500 `INTERNAL_ERROR`.
- Details per endpoint: [05-endpoint-detail-template.md](05-endpoint-detail-template.md).

| Tag | Method | Path | Summary | Success | Errors | Notes |
|---|---|---|---|---|---|---|
| Health | GET | `/health` | Liveness check | 200 | — | Public |
| Auth | POST | `/api/auth/token` | Sign in with email and password | 200 | 400, 401 | Public; MFA still required |
| Auth | POST | `/api/auth/mfa/verify` | Complete MFA for the current session | 200 | 400, 401 | Bearer of the unverified token |
| Auth | POST | `/api/auth/refresh` | Exchange a refresh token for a new session | 200 | 400, 401 | Public |
| Auth | POST | `/api/auth/logout` | Revoke the current session | 204 | — | Public |
| Registration | POST | `/api/registration/start` | Step 1: choose a login (email and password) | 201 | 400, 409 | Public |
| Registration | POST | `/api/registration/verify-identity` | Step 2: match account number, postal code, date of birth | 200 | 400, 404, 409, 422 | Public |
| Registration | GET | `/api/registration/security-questions` | Questions to choose from in step 3 | 200 | — | Public; Lang |
| Registration | POST | `/api/registration/security-questions` | Step 3: answer two security questions | 200 | 400, 404, 409 | Public |
| Registration | POST | `/api/registration/trusted-contact` | Step 5 (optional): add a trusted contact | 200 | 400, 404, 409 | Public |
| Registration | POST | `/api/registration/complete` | Final step: create the login | 201 | 400, 404, 409 | Public; returns signed-in session |
| Dashboard | GET | `/api/wealth/dashboard` | Portfolio summary, allocation, goals, alerts, quick links | 200 | 401, 404 | 🔒 Lang |
| Accounts | GET | `/api/wealth/accounts` | List visible accounts | 200 | 400, 401 | 🔒 Paged |
| Accounts | POST | `/api/wealth/accounts` | Open a new, empty account | 201 | 400, 401 | 🔒 |
| Accounts | GET | `/api/wealth/accounts/{id}` | Account detail with holdings | 200 | 401, 404 | 🔒 |
| Accounts | PATCH | `/api/wealth/accounts/{id}` | Update nickname or visibility | 200 | 400, 401, 404 | 🔒 |
| Accounts | GET | `/api/wealth/accounts/{id}/transactions` | Posted transactions (paged) + upcoming scheduled | 200 | 400, 401, 404 | 🔒 Paged; filter `type` |
| Accounts | GET | `/api/wealth/accounts/{id}/performance` | Performance series with benchmark | 200 | 400, 401, 404 | 🔒 |
| Goals | GET | `/api/wealth/goals` | List savings goals | 200 | 400, 401 | 🔒 Paged |
| Goals | POST | `/api/wealth/goals` | Create a savings goal | 201 | 400, 401 | 🔒 |
| Documents | GET | `/api/wealth/documents` | List documents | 200 | 400, 401 | 🔒 Paged; filters `year`, `category` |
| Documents | GET | `/api/wealth/documents/{id}` | Document download stub (base64 PDF) | 200 | 401, 404 | 🔒 |
| Documents | POST | `/api/wealth/documents/{id}/share` | Share by email, link expires in 7 days | 200 | 400, 401, 404 | 🔒 |
| Profile | GET | `/api/profile` | Signed-in user profile | 200 | 401, 404 | 🔒 |
| Profile | PUT | `/api/profile/address` | Replace the mailing address | 200 | 400, 401, 404 | 🔒 |
| Profile | GET | `/api/preferences` | User preferences | 200 | 401, 404 | 🔒 |
| Profile | PUT | `/api/preferences` | Update preferences (partial) | 200 | 400, 401, 404 | 🔒 |
| Notifications | GET | `/api/notifications` | All notifications, newest first | 200 | 401 | 🔒 |
| Notifications | POST | `/api/notifications/{id}/read` | Mark one as read | 200 | 401, 404 | 🔒 |
| Notifications | POST | `/api/notifications/read-all` | Mark all as read | 200 | 401 | 🔒 |
| Education | GET | `/api/education/content` | Learning catalogue with progress | 200 | 400, 401 | 🔒 Lang; filters `level`, `type` |
| Education | GET | `/api/education/glossary` | Financial glossary A–Z | 200 | 400, 401 | 🔒 Lang; search `q` |
| Education | POST | `/api/education/progress` | Save progress on a class or challenge | 200 | 400, 401, 404 | 🔒 |
| Advisor | GET | `/api/advisor/availability` | Open slots over the next five business days | 200 | 400, 401, 404 | 🔒 |
| Advisor | GET | `/api/advisor/appointments` | The user's meetings, oldest first | 200 | 401 | 🔒 |
| Advisor | POST | `/api/advisor/appointments` | Book a meeting in an open slot | 201 | 400, 401, 404, 409 | 🔒 |
| Advisor | PATCH | `/api/advisor/appointments/{id}` | Reschedule or cancel | 200 | 400, 401, 404, 409 | 🔒 |
| Advisor | GET | `/api/advisor/messages` | Secure message thread, oldest first | 200 | 401 | 🔒 |
| Advisor | POST | `/api/advisor/messages` | Send a message to the advisor | 201 | 400, 401, 404 | 🔒 |
| Market | GET | `/api/market/quote` | Quotes with held/watched flags | 200 | 401 | 🔒 `symbols` filter |
| Market | GET | `/api/market/securities/{symbol}` | One security with quote and description | 200 | 400, 401, 404 | 🔒 |
| Market | GET | `/api/market/history` | Closing prices over a range | 200 | 400, 401, 404 | 🔒 |
| Market | GET | `/api/market/search` | Find securities by symbol or name | 200 | 400, 401 | 🔒 search `q` |
| Market | GET | `/api/market/news` | Headlines, newest first | 200 | 401 | 🔒 |
| Market | GET | `/api/market/roundup` | This week's market roundup | 200 | 401 | 🔒 |
| Market | GET | `/api/market/baskets` | Thematic baskets | 200 | 401 | 🔒 |
| Market | GET | `/api/market/watchlist` | Watchlist quotes, in order added | 200 | 401 | 🔒 |
| Market | PUT | `/api/market/watchlist/{symbol}` | Add to watchlist (no-op if present) | 200 | 400, 401, 404 | 🔒 |
| Market | DELETE | `/api/market/watchlist/{symbol}` | Remove from watchlist | 200 | 400, 401, 404 | 🔒 |

Total: 49 operations.

**Deprecated endpoints (do not use for new integrations):**

None. Note: the `meetingType` query parameter of `GET /api/advisor/availability` is accepted only for compatibility
and has no effect.

**OpenAPI source of truth:** [openapi.yaml](../openapi.yaml) (also `GET http://localhost:4200/openapi.json`) — use it
to generate client code; treat the catalog above as a navigational aid, not a spec.
