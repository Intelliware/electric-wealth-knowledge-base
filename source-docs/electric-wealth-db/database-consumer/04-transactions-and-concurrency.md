---
slug: transactions-and-concurrency
title: "Transactions and Concurrency"
part: B2
source-commit: 05ff1a61e87f62666c0fceb35fc1604c1d7abccd
---

## 22. Transactions and Concurrency

### 22.1 Transaction Best Practices

**SQLite concurrency model (as configured):**
- **Single writer, many readers.** `journal_mode` is the default `delete` (rollback journal, not WAL), so a writer holds an exclusive lock on the whole file while it commits.
- Every transaction is **serializable**; there is no isolation level to choose.
- Waiting for a lock is bounded by `busy_timeout=3000` ms on the API's connections. After that the statement fails with `SQLITE_BUSY` ("database is locked").
- The API's HikariCP pool has at most **4** connections.

**Rule:** any operation that writes to more than one row or table, or reads and then writes based on what it read, must run in **one transaction**. The API does this with Spring `@Transactional`:

| Operation | Statements | Where |
|---|---|---|
| Refresh-token rotation | SELECT old → UPDATE revoke → INSERT new session | `SessionRepository.rotate` |
| Open account | SELECT `MAX(sort_order)` → INSERT `accounts` → INSERT `account_performance` | `AccountRepository.open` |
| Create goal | SELECT `MAX(sort_order)` → INSERT `goals` | `GoalRepository.create` |
| Save preferences | Multi-column UPDATE(s) on `user_preferences` | `ProfileRepository` (line 77) |
| Save security answers | DELETE all → INSERT × n → UPDATE flag | `RegistrationRepository.saveSecurityAnswers` |
| Complete registration | 8+ INSERTs/UPDATEs across 9 tables | `RegistrationRepository.complete`: "a failure leaves nothing half-created" |

```java
// Spring
@Transactional
public String open(String userId, AccountOpenRequest input) {
    int next = jdbc.sql("SELECT COALESCE(MAX(sort_order), 0) + 1 FROM accounts WHERE user_id = ?") …;
    jdbc.sql("INSERT INTO accounts …").update();
    jdbc.sql("INSERT INTO account_performance …").update();
    return id;
}
```

```js
// Node (node:sqlite has no helper; this is the pattern in scripts/lib.js)
db.exec('BEGIN;');
try { /* statements */ db.exec('COMMIT;'); }
catch (e) { db.exec('ROLLBACK;'); throw e; }
```

**Single-statement writes** (mark notification read, rename account, watchlist add/remove, cancel appointment) run in auto-commit and need no explicit transaction.

### 22.2 Concurrency Patterns Used

| Concern | Pattern | Example |
|---|---|---|
| Two users booking the same advisor slot | **Let the database decide.** The partial unique index `ux_appointments_advisor_slot (advisor_id, date, time) WHERE status='scheduled'` rejects the loser, and the API maps the error to HTTP 409. The availability check before the insert is only a UX hint. | `AdvisorController.bookingSlot` |
| Double-submitting "add to watchlist" / "save progress" | **Idempotent upsert** with `ON CONFLICT … DO NOTHING / DO UPDATE` | `MarketRepository`, `EducationRepository` |
| Read-then-write ordering (`MAX(sort_order)+1`) | Wrap in `@Transactional`. `sort_order` is not unique, so a race would only produce a duplicate position, never an error. | `AccountRepository.open`, `GoalRepository.create` |
| Token reuse after refresh | Rotation in one transaction, filtered on `revoked = 0` | `SessionRepository.rotate` |

### 22.3 Avoiding Deadlocks and Lock Timeouts

SQLite does not deadlock in the classic multi-row sense, but it can return `SQLITE_BUSY`:
- **Keep write transactions short.** Do no network or user I/O inside them.
- **Avoid upgrading a read transaction to a write** while another connection is also reading then writing. Two deferred transactions that both read and then try to write can make one fail immediately with `SQLITE_BUSY` (the busy handler is not invoked in that case). If you write a new read-then-write sequence that must not fail, start it with `BEGIN IMMEDIATE`.
- **Do not hold a DBeaver transaction open** (auto-commit off) against the live file while the API runs.
- **Never run `npm run db:reset` / `db:migrate` / `db:seed` while the API is running.**
