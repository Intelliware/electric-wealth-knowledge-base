---
title: "Request Format"
block: reverse-eng/api/consumer/request-format
part: B2
source_repo: electric-wealth-api-spring
source_version: 0.1.0
source_commit: 8a61cdeafb4537b16221d7aa0e9300010abd3387
extracted: 2026-10-15
---

## 6. Request Format

**Required request headers:**

| Header | When | Notes |
|---|---|---|
| `Authorization: Bearer <token>` | Protected endpoints and `POST /api/auth/mfa/verify` | See [02-authentication.md](02-authentication.md) |
| `Content-Type: application/json` | Every request with a body | Any other type → 400 `(root): the request body must be JSON (Content-Type: application/json)` |
| `Accept-Language` | Optional | Starts with `fr` → French (`fr-CA`); anything else or absent → English (`en-CA`). Affects dashboard quick-link labels, education titles, glossary, security questions. |

**Body encoding:**

- JSON, UTF-8. Field names are camelCase.
- Money as integer cents (`500000` = $5,000.00).
- Dates as `YYYY-MM-DD`; times as `HH:MM` (24-hour).
- Enum values exactly as listed in the spec (case-sensitive), e.g. `in_person`, `Vacation`, `TFSA`, `en-CA`.
- Optional fields may be omitted. On `PATCH /api/wealth/accounts/{id}`, `"nickname": null` clears the nickname while omitting it leaves it unchanged.
- Leading/trailing whitespace in text fields is trimmed before validation (so `"   "` counts as empty).

**Query string conventions:**

- Enum filters use the same JSON values (`?type=dividend`, `?level=beginner`); an empty value is treated as absent.
- Lists as comma-separated values (`?symbols=XEQT.TO,AAPL`).
- Paging: `page` (≥1), `pageSize` (1–100).

**Path conventions:**

Ids are opaque strings; market symbols are case-insensitive and normalised to upper case.

**Size limits:**

No overall body size limit. Per-field limits apply (e.g. advisor message 2000 chars, appointment notes 500,
glossary `q` 60, market search `q` 40) — see [05-endpoint-detail-template.md](05-endpoint-detail-template.md).

**Idempotency keys:**

Not supported. Do not blindly retry non-idempotent `POST`s (account open, goal create, booking, message, share,
registration start) — see [11-error-handling.md](11-error-handling.md).

**Request ID propagation:**

The API does not read an incoming request id. It generates its own per request and returns it in `X-Trace-Id`
(and `error.traceId`). Log that value on the client.

**Common mistakes:**

- Sending the step-1 token from `/api/auth/token` to protected endpoints before MFA (→ 401).
- Sending dollars instead of cents.
- Using enum constant names instead of JSON values (`IN_PERSON` instead of `in_person`).
- Omitting `Content-Type: application/json`.
