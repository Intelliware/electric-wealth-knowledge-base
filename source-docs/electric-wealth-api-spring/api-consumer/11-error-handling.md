---
title: "Error Handling and Retries"
block: reverse-eng/api/consumer/error-handling
part: B2
source_repo: electric-wealth-api-spring
source_version: 0.1.0
source_commit: 8a61cdeafb4537b16221d7aa0e9300010abd3387
extracted: 2026-10-15
---

## 11. Error Handling and Retries

**Canonical error envelope:**

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request. targetDate: Must be a future date",
    "traceId": "trace-1a2b3c4d"
  }
}
```

Schema `ErrorEnvelope` in [openapi.yaml](../openapi.yaml). All codes:

| Status | Code | Meaning |
|---|---|---|
| 400 | `VALIDATION_ERROR` | Invalid body/params/JSON/content type |
| 401 | `UNAUTHORIZED` | Missing, expired, revoked or not-MFA-verified token |
| 401 | `INVALID_CREDENTIALS` | Wrong email or password |
| 401 | `INVALID_MFA` | Wrong MFA code |
| 401 | `INVALID_REFRESH` | Refresh token unknown, used or revoked |
| 404 | `NOT_FOUND` | Resource missing / not yours; unknown route; wrong method |
| 404 | `NO_ADVISOR` | No advisor assigned to the client |
| 404 | `REGISTRATION_NOT_FOUND` | Registration expired (30 min), unknown or finished |
| 409 | `EMAIL_TAKEN` | Email already has online access |
| 409 | `ALREADY_ENROLLED` | Client already has online access |
| 409 | `STEP_OUT_OF_ORDER` | Enrolment step done before its prerequisite |
| 409 | `SLOT_UNAVAILABLE` | Advisor slot not offered or just taken |
| 409 | `NOT_SCHEDULED` | Appointment already completed/cancelled |
| 422 | `IDENTITY_MISMATCH` | Account number / postal code / date of birth did not match |
| 429 | `RATE_LIMITED` | Too many requests |
| 500 | `INTERNAL_ERROR` | Unexpected server error |

**Status code → retry decision matrix:**

| Status | Retry? | Action |
|---|---|---|
| 400, 422 | No | Fix the input; show the message |
| 401 `UNAUTHORIZED` | Once, after refresh | Refresh tokens, retry; if it fails again, sign in |
| 401 other | No | Ask the user to re-enter credentials / code |
| 404 | No | Treat as gone or not yours; for `REGISTRATION_NOT_FOUND` restart enrolment |
| 409 `SLOT_UNAVAILABLE` | No | Reload availability, let the user pick again |
| 409 other | No | Follow the message (sign in, finish the earlier step) |
| 429 | Yes, after ≤60 s | Backoff with jitter |
| 500 | Only idempotent requests | Up to 2 retries with backoff; report the `traceId` |
| Network error / timeout | Only idempotent requests | Same as 500 |

**Retry algorithm (recommended):**

```ts
async function call(req, attempt = 0) {
  const res = await fetch(req);
  if (res.status === 429 || (res.status >= 500 && isIdempotent(req))) {
    if (attempt < 2) { await sleep(1000 * 2 ** attempt + Math.random() * 250); return call(req, attempt + 1); }
  }
  return res;
}
```

**Idempotency and retries:**

No idempotency keys. Retrying a non-idempotent `POST` after a timeout can create duplicates (two goals, two
messages). Booking twice is protected by the slot rule (the second gets 409). Check state (list endpoint) before
retrying a create.

**Validation errors (400/422):**

`message` is `Invalid request. ` followed by one entry per failing field, `field: reason`, separated by `; `:

```text
Invalid request. grantType: must be "password"; username: must be a well-formed email address; password: must not be empty
```

Field paths use dots for nesting and brackets for list items from Bean Validation (`address.postalCode`,
`answers[0].answer`); type errors from JSON parsing use dots for both (`answers.0.answer: invalid value`);
`(root)` means the whole body. Field order in the message is not guaranteed. There is no machine-readable
per-field array — parse on `; ` and the first `:` if you need field mapping.

**Distributed-tracing hooks:**

Every response carries `X-Trace-Id`; errors repeat it as `error.traceId`. Include it in client logs and bug
reports; it appears in the server's request log line for that call.

**Anti-patterns:**

- Branching on `message` text instead of `code`.
- Retrying 400/401/404/409.
- Treating 404 on another user's id as "permission denied" — the API intentionally does not distinguish.
