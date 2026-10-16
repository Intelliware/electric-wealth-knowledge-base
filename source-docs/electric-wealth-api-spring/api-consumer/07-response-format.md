---
title: "Response Format"
block: reverse-eng/api/consumer/response-format
part: B2
source_repo: electric-wealth-api-spring
source_version: 0.1.0
source_commit: 8a61cdeafb4537b16221d7aa0e9300010abd3387
extracted: 2026-10-15
---

## 7. Response Format

**Status code policy:**

2xx bodies are the resource itself (no success envelope); errors always use the error envelope.

**Specific status codes used:**

| Status | Meaning here |
|---|---|
| 200 | Success (reads, updates, actions) |
| 201 | Created: registration start/complete, account open, goal create, appointment booking, advisor message |
| 204 | Logout (no body) |
| 400 | `VALIDATION_ERROR` — invalid body, parameter, header or JSON |
| 401 | Not signed in / bad credentials / bad MFA / bad refresh token |
| 404 | Resource missing **or owned by another user**; unknown route; wrong HTTP method on a known path |
| 409 | Conflict with current state (slot taken, step order, email taken, already enrolled, appointment not scheduled) |
| 422 | `IDENTITY_MISMATCH` during enrolment |
| 429 | `RATE_LIMITED` |
| 500 | `INTERNAL_ERROR` |

403 and 405 are never returned.

**Success envelope:**

None. Paged lists return `{ "items": [...], "page": 1, "pageSize": 20, "total": 42 }` (transactions add
`"scheduled": [...]`); other lists return a bare JSON array.

**Always-present response headers:**

| Header | Value |
|---|---|
| `X-Trace-Id` | `trace-xxxxxxxx`, also in error bodies |
| `Content-Type` | `application/json` (except 204) |

**Error envelope:**

```json
{ "error": { "code": "SLOT_UNAVAILABLE", "message": "That time is no longer available. Please pick another slot.", "traceId": "trace-3f9a12bc" } }
```

**Error code stability:**

`code` values are stable identifiers shared with the Node API and safe to branch on. `message` is human-readable;
validation messages may change wording (they are generated per field as `field: message` joined by `; `).

**Content negotiation:**

JSON only. `Accept-Language` changes text content, not the format.

**Forward-compatibility rules:**

- Optional fields are **omitted** when they have no value (not sent as `null`). A few fields are always present and may be `null`: `AccountSummary.nickname`, `AccountSummary.contributionRoom`, `Document.accountId`.
- Whole-number decimals are written without `.0` (`48`, not `48.0`) — parse percentages as numbers, not by type.
- Timestamps are ISO-8601 UTC strings (`2026-10-14T17:49:35Z`; session-related ones have milliseconds).
- Ignore unknown fields; new optional fields may be added.
