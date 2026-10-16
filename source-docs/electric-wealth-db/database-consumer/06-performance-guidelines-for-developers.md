---
slug: performance-guidelines-for-developers
title: "Performance Guidelines for Developers"
part: B2
source-commit: 05ff1a61e87f62666c0fceb35fc1604c1d7abccd
---

## 24. Performance Guidelines for Developers

### 24.1 Query Performance Tips

The seeded database is tiny (~440 KB; the largest table has 408 rows), so performance problems will come from **access patterns**, not volume. Follow these rules so queries stay index-backed as data grows:

1. **Lead with `user_id`.** Every tenant table's primary key starts with `user_id`, so `WHERE user_id = ?` (plus `AND id = ?` / `AND account_id = ?`) is an index range scan.
2. **Sort along an existing index** where one exists:

   | Query | Index that serves it |
   |---|---|
   | Transactions newest-first | `ix_transactions_account_date (user_id, account_id, date DESC)` |
   | Notifications newest-first | `ix_notifications_user_created (user_id, created_at DESC)` |
   | Appointments by date/time | `ix_appointments_user (user_id, date, time)` |
   | Advisor message thread | `ix_advisor_messages_user (user_id, sent_at)` |
   | Price chart | PK `(symbol, range, t)` |
   | Advisor's scheduled slots | `ux_appointments_advisor_slot (advisor_id, date, time) WHERE status='scheduled'`. Include `status = 'scheduled'` in the query so the planner can use the partial index. |

   Adding an `, id` tie-breaker (as the API does) costs a small temp sort; acceptable at these sizes.
3. **Avoid N+1.** Fetch a list in one query; use `JOIN` / `EXISTS` (see the quotes pattern in Common Query Patterns) rather than a query per row. Two queries (summary + series) is fine for detail views.
4. **Use `COUNT(*)` + `LIMIT/OFFSET` paging** with the same `WHERE` as the page query. Per-user sets are small, so OFFSET cost is negligible.
5. **Leading-wildcard `LIKE '%q%'` cannot use an index.** It is acceptable for the 6-row `securities` and 7-row `glossary_terms`, but do not apply it to tables that grow (`transactions`, `notifications`).
6. **Batch writes in one transaction.** In SQLite every auto-commit statement is its own fsync, so 100 inserts in one `BEGIN … COMMIT` are far faster than 100 auto-commits.
7. **Keep write transactions short.** A writer locks the whole file (rollback-journal mode).
8. **Don't read the whole table to find one "latest" row** in new code. `SELECT MAX(week_of) FROM market_roundup_items` is a full scan; add an index if such a table grows.

**Example EXPLAIN (SQLite):**
```sql
EXPLAIN QUERY PLAN
SELECT * FROM transactions
WHERE user_id = 'sarah' AND account_id = 'tfsa' AND status = 'posted'
ORDER BY date DESC, id LIMIT 20 OFFSET 0;
-- SEARCH transactions USING INDEX ix_transactions_account_date (user_id=? AND account_id=?)
-- USE TEMP B-TREE FOR LAST TERM OF ORDER BY

EXPLAIN QUERY PLAN
SELECT * FROM accounts WHERE user_id = 'sarah' AND hidden = 0 ORDER BY sort_order LIMIT 20 OFFSET 0;
-- SEARCH accounts USING INDEX sqlite_autoindex_accounts_1 (user_id=?)
-- USE TEMP B-TREE FOR ORDER BY
```
(Plans captured with sqlite3 3.51.0 on 2026-10-15.)

---

# Part 9 — Reference

See the [data dictionary](../data-dictionary.md) and the [ERD](../erd.mmd).
