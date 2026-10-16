---
title: "Changelog and Deprecation"
block: reverse-eng/api/consumer/changelog-and-deprecation
part: B2
source_repo: electric-wealth-api-spring
source_version: 0.1.0
source_commit: 8a61cdeafb4537b16221d7aa0e9300010abd3387
extracted: 2026-10-15
---

## 15. Changelog and Deprecation

**Format:**

The API repo keeps no changelog file. Entries below follow Keep a Changelog (`Added / Changed / Deprecated /
Removed / Fixed / Security`), reconstructed from the source at the documented commit.

**Recent releases:**

### 0.1.0 — documented at commit `8a61cde` (2026-10-15)

#### Added

- 49 operations: health, auth (token, MFA, refresh, logout), registration (6 steps), dashboard, accounts (6), goals (2), documents (3), profile/preferences (4), notifications (3), education (3), advisor (6), market (10).
- Committed OpenAPI 3.1.0 contract, matching the Node API's contract for every implemented endpoint.

#### Known differences from the Node API

- A body over 100 kB is accepted (the Node API answers 413 `PAYLOAD_TOO_LARGE`).
- A JSON body with a non-UTF-8 charset is parsed (the Node API answers 415 `UNSUPPORTED_MEDIA_TYPE`).
- Validation message wording differs (codes and statuses are the same).

**Currently deprecated (active deprecation list):**

| Item | Note |
|---|---|
| `meetingType` query on `GET /api/advisor/availability` | Accepted for compatibility; has no effect |

**Removed (history of breaking changes by major version):**

None recorded.

**Subscription channels:**

None. Watch the `electric-wealth-api-spring` repository (`openapi.json`, `README.md`).

**How consumers should track this:**

Diff `openapi.json` between commits; the API's test suite guarantees it matches the running code.
