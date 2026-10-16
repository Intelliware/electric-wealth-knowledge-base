---
slug: error-handling-and-recovery
title: "Error Handling and Recovery"
part: B2
source-commit: 05ff1a61e87f62666c0fceb35fc1604c1d7abccd
---

## 23. Error Handling and Recovery

### 23.1 Common Errors and Solutions

SQLite reports constraint failures as result code **19 `SQLITE_CONSTRAINT`**. In Spring, sqlite-jdbc's `SQLiteException` is wrapped in a `DataAccessException` (usually `DataIntegrityViolationException`); the raw text is on `e.getMostSpecificCause().getMessage()`.

| Error message (SQLite) | Cause in this schema | Solution |
|-------|-------|----------|
| `UNIQUE constraint failed: users.email` | Email already registered (case-insensitive) | Check `SELECT 1 FROM users WHERE email = ?` first, as the API's `emailInUse` does |
| `UNIQUE constraint failed: appointments.advisor_id, appointments.date, appointments.time` | Advisor slot already has a `scheduled` meeting | Expected under concurrency. Return "slot unavailable" (the API returns 409 `SLOT_UNAVAILABLE`). |
| `UNIQUE constraint failed: <table>.user_id, <table>.id` | Duplicate tenant-scoped id | Generate a new id, or use `ON CONFLICT` if the write is meant to be an upsert |
| `FOREIGN KEY constraint failed` (INSERT/UPDATE) | Referenced parent missing: unknown `account_id`, `advisor_id`, `symbol`, `question_id`, `content_id` | Validate the parent exists, e.g. `EducationRepository.contentExists`, `RegistrationRepository.unknownQuestionIds` |
| `FOREIGN KEY constraint failed` (DELETE) | Deleting a `users` / `advisors` / `security_questions` / `offline_clients` row that is still referenced by a NO ACTION FK | Clear or delete the referencing rows first. See the Part A FAQ. |
| `CHECK constraint failed: <expression>` | Value outside an enumeration or range, e.g. `accounts.type`, `progress_pct BETWEEN 0 AND 100` | Validate against the API enums before writing. The message quotes the failing expression. |
| `NOT NULL constraint failed: <table>.<column>` | Missing required value. Note that `user_preferences` columns have **no defaults**. | Supply every NOT NULL column |
| `database is locked` / `SQLITE_BUSY` (code 5) | Another connection held the write lock longer than `busy_timeout` (3 s) | Retry; shorten transactions; stop external tools writing to the file |
| `no such table: …` / `no such column: …` | Database built from an older migration set | `npm run db:migrate` (or `db:reset`) in `electric-wealth-db` |
| API startup: `Database not found at …` | File not built or wrong `DB_PATH` | `npm run db:reset` |

### 23.2 Retry Strategies

| Error class | Retry? | Approach |
|---|---|---|
| `SQLITE_CONSTRAINT` (19): UNIQUE, FK, CHECK, NOT NULL | **No** | Deterministic; map to a 4xx business error (`409` for slot or email conflicts, `400`/`422` for validation) |
| `SQLITE_BUSY` (5) | Yes, briefly | The driver already waits up to 3 s. One application-level retry with a short backoff is reasonable for idempotent operations. |
| Missing file / schema | No | Operator action (`db:reset` / `db:migrate`) |

```java
// Spring: translate the one constraint the API treats as an expected business outcome
try {
    return change.get();
} catch (DataAccessException e) {
    if (String.valueOf(e.getMostSpecificCause().getMessage()).contains("UNIQUE constraint failed")) {
        throw new ApiException(HttpStatus.CONFLICT, "SLOT_UNAVAILABLE",
                "That time is no longer available. Please pick another slot.");
    }
    throw e;   // everything else → GlobalExceptionHandler
}
```

### 23.3 Recovery

The database is a rebuildable artifact. For any corrupted, half-migrated or polluted local state:
```bash
# stop the API, close DBeaver
cd electric-wealth-db && npm run db:reset && npm run db:status
```
Runtime-created data (sessions, registrations, bookings, new accounts and goals) is lost by design.
