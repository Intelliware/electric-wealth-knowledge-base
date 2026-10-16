---
slug: common-query-patterns
title: "Common Query Patterns"
part: B2
source-commit: 05ff1a61e87f62666c0fceb35fc1604c1d7abccd
---

## 20. Common Query Patterns

Every pattern below is taken from the Spring API repositories (`electric-wealth-api-spring/src/main/java/com/electricwealth/api/repository/`) and runs against schema version `007`. `?` / `:name` are bound parameters; never concatenate user input into SQL.

### 20.1 Authentication

```sql
-- Look up a login (email is COLLATE NOCASE, so no LOWER() is needed)  [UserRepository]
SELECT * FROM users WHERE email = ?;

-- Validate a bearer token  [SessionRepository.findActive]
SELECT * FROM sessions WHERE access_token = ?;
-- then, in code: revoked = 0 AND expires_at > now

-- Refresh-token rotation (ONE transaction)  [SessionRepository.rotate]
SELECT * FROM sessions WHERE refresh_token = ? AND revoked = 0;
UPDATE sessions SET revoked = 1 WHERE access_token = ?;
INSERT INTO sessions (access_token, refresh_token, user_id, mfa_verified, expires_at, created_at)
VALUES (?, ?, ?, ?, ?, ?);
```

### 20.2 Fetching a User's Accounts (paged)

```sql
-- [AccountRepository.listVisible]
SELECT COUNT(*) FROM accounts WHERE user_id = ? AND hidden = 0;
SELECT * FROM accounts WHERE user_id = ? AND hidden = 0
ORDER BY sort_order LIMIT ? OFFSET ?;          -- OFFSET = (page - 1) * pageSize

-- Portfolio total = sum of VISIBLE balances (minor units)
SELECT COALESCE(SUM(balance), 0) FROM accounts WHERE user_id = ? AND hidden = 0;
```

### 20.3 Filtering and Pagination (transactions)

```sql
-- Posted history, optional type filter, newest first  [AccountRepository.listPostedTransactions]
SELECT COUNT(*) FROM transactions
WHERE user_id = :userId AND account_id = :accountId AND status = 'posted' [AND type = :type];

SELECT * FROM transactions
WHERE user_id = :userId AND account_id = :accountId AND status = 'posted' [AND type = :type]
ORDER BY date DESC, id
LIMIT :limit OFFSET :offset;                   -- uses ix_transactions_account_date

-- Upcoming
SELECT * FROM transactions WHERE user_id = ? AND account_id = ? AND status = 'scheduled' ORDER BY date, id;
```

### 20.4 Account Detail: Holdings and Performance

```sql
SELECT * FROM holdings WHERE user_id = ? AND account_id = ? ORDER BY weight_pct DESC, symbol;

SELECT * FROM account_performance WHERE user_id = ? AND account_id = ?;
SELECT series, period, value FROM performance_points
WHERE user_id = ? AND account_id = ? ORDER BY period;
-- split in code: series='account' (minor units) vs series='benchmark' (index, start = 100)
```

### 20.5 Dashboard

```sql
SELECT currency, day_change, day_change_pct, as_of FROM portfolio_snapshots WHERE user_id = ?;
SELECT asset_class, pct FROM asset_allocations WHERE user_id = ? ORDER BY sort_order;
SELECT id, name, progress_pct FROM goals WHERE user_id = ? AND show_on_dashboard = 1 ORDER BY sort_order;
SELECT id, type, title, deep_link FROM alerts WHERE user_id = ? ORDER BY id;
SELECT label, deep_link FROM quick_links WHERE user_id = ? ORDER BY sort_order;
```

### 20.6 Documents (optional filters)

```sql
SELECT * FROM documents
WHERE user_id = :userId [AND year = :year] [AND category = :category]
ORDER BY year DESC, action_required DESC, id
LIMIT :limit OFFSET :offset;

-- Record a 7-day share  [DocumentRepository.share]
INSERT INTO document_shares (user_id, document_id, recipient, created_at, expires_at) VALUES (?, ?, ?, ?, ?);
```

### 20.7 Notifications

```sql
SELECT * FROM notifications WHERE user_id = ? ORDER BY created_at DESC, id;
UPDATE notifications SET read = 1 WHERE user_id = ? AND id = ?;          -- mark one
UPDATE notifications SET read = 1 WHERE user_id = ? AND read = 0;        -- mark all
```

### 20.8 Localised Catalogue with Per-User Progress (LEFT JOIN + default)

```sql
-- [EducationRepository]  pick title_en or title_fr from the request language
SELECT c.id, c.title_fr AS title, c.level, c.type, c.duration_min,
       COALESCE(p.progress_pct, 0) AS progress_pct          -- no row = 0%
FROM education_content c
LEFT JOIN education_progress p ON p.content_id = c.id AND p.user_id = :userId
WHERE 1 = 1 [AND c.level = :level] [AND c.type = :type]
ORDER BY c.sort_order, c.id;

-- Upsert progress
INSERT INTO education_progress (user_id, content_id, progress_pct, updated_at)
VALUES (?, ?, ?, strftime('%Y-%m-%dT%H:%M:%SZ', 'now'))
ON CONFLICT (user_id, content_id)
DO UPDATE SET progress_pct = excluded.progress_pct, updated_at = excluded.updated_at;
```

### 20.9 Text Search with Escaped LIKE

```sql
-- [MarketRepository / EducationRepository]; the bound value is '%' + escape(q) + '%', escaping \ % _
SELECT symbol, name, exchange, currency FROM securities
WHERE symbol LIKE ? ESCAPE '\' OR name LIKE ? ESCAPE '\';
```
Sort accented text in application code with a locale collator. SQLite's `NOCASE` is ASCII-only and puts `é` after `z`.

### 20.10 Market Quotes with Per-User Flags (correlated EXISTS)

```sql
SELECT s.*,
  EXISTS (SELECT 1 FROM holdings h JOIN accounts a ON a.user_id = h.user_id AND a.id = h.account_id
          WHERE h.user_id = :userId AND h.symbol = s.symbol AND a.hidden = 0) AS held,
  EXISTS (SELECT 1 FROM watchlist w WHERE w.user_id = :userId AND w.symbol = s.symbol) AS watched
FROM securities s
[WHERE s.symbol IN (:symbols)]
ORDER BY s.symbol;

-- Price chart
SELECT t, close FROM price_history WHERE symbol = ? AND range = ? ORDER BY t;   -- range: 1D|1M|1Y|5Y

-- Watchlist add (idempotent) / remove
INSERT INTO watchlist (user_id, symbol, added_at) VALUES (?, ?, strftime('%Y-%m-%dT%H:%M:%SZ', 'now'))
ON CONFLICT (user_id, symbol) DO NOTHING;
DELETE FROM watchlist WHERE user_id = ? AND symbol = ?;
```

### 20.11 Advisor Availability and Booking

```sql
-- The user's advisor
SELECT a.* FROM advisors a JOIN users u ON u.advisor_id = a.id WHERE u.id = ?;

-- Taken slots in the booking window (dates are Toronto local, YYYY-MM-DD)
SELECT date, time FROM appointments
WHERE advisor_id = ? AND status = 'scheduled' AND date BETWEEN ? AND ?;

-- Book (may fail with UNIQUE on ux_appointments_advisor_slot; treat that as "slot taken")
INSERT INTO appointments (id, user_id, advisor_id, meeting_type, date, time, status, notes, created_at)
VALUES (?, ?, ?, ?, ?, ?, 'scheduled', ?, ?);

-- Reschedule / cancel (cancel frees the slot because the unique index is partial)
UPDATE appointments SET date = ?, time = ? WHERE user_id = ? AND id = ?;
UPDATE appointments SET status = 'cancelled' WHERE user_id = ? AND id = ?;

-- Message thread
SELECT * FROM advisor_messages WHERE user_id = ? ORDER BY sent_at, id;
```

### 20.12 Online Enrolment (multi-step)

```sql
-- 1. start
INSERT INTO registrations (id, email, password, created_at, expires_at) VALUES (?, ?, ?, ?, ?);  -- expires +30 min
-- 2. identity: fetch the branch record, then compare postal code (spaces stripped, upper-case) and DOB in code
SELECT * FROM offline_clients WHERE account_number = ?;
UPDATE registrations SET account_number = ? WHERE id = ?;
-- 3. security answers (replace-all, one transaction; answers normalised to trimmed lower-case)
DELETE FROM registration_security_answers WHERE registration_id = ?;
INSERT INTO registration_security_answers (registration_id, question_id, answer) VALUES (?, ?, ?);  -- × n
UPDATE registrations SET security_answers_saved = 1 WHERE id = ?;
-- 4. complete: ONE transaction creates users, user_profiles, user_preferences, user_security_answers
--    (INSERT … SELECT from registration_security_answers), portfolio_snapshots, a 'welcome' notification,
--    3 quick_links, then sets offline_clients.enrolled_user_id and registrations.user_id/mfa_method/consented_at.
```
A registration is **open** when `user_id IS NULL AND expires_at > now`.
