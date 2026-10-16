---
title: "Electric Wealth Database — Data Dictionary"
part: B1
source-repo: electric-wealth-db
source-commit: 05ff1a61e87f62666c0fceb35fc1604c1d7abccd
schema-version: 007_registration.sql
engine: SQLite 3
extracted: 2026-10-15
---

# Electric Wealth Database — Data Dictionary

The authoritative reference for every table and column in the `electric-wealth-db` SQLite schema (migrations 001–007). The relationship diagram is in [`erd.mmd`](./erd.mmd); how to use the data is in [`database-consumer/`](./database-consumer/).

**Legend:**
- **Key:** `PK` primary key (`PK1`, `PK2` … for composite), `FK→t.c` foreign key, `UQ` unique.
- **Null:** `N` = NOT NULL, `Y` = nullable.
- **ON DELETE:** stated on every FK. Every FK is `ON UPDATE NO ACTION`.

**Conventions:**
- **Money** = INTEGER minor units (cents) of the row's `currency` (ISO-4217).
- **Booleans** = INTEGER `0`/`1` with a CHECK.
- **Timestamps** = ISO-8601 UTC TEXT (`2024-12-01T13:30:00Z`; `sessions` uses milliseconds), except `appointments.date`/`time`, which are **Toronto local**.
- **`_pct`** = REAL percent (`0.65` = 0.65%), except `education_progress.progress_pct` (INTEGER 0–100).
- **Ids** in tables whose PK starts with `user_id` are unique **per user only**.

## Table Index

| Domain | Table | Rows (seed) | Purpose |
|---|---|---|---|
| Identity | [`users`](#users) | 6 | Online login; hub of all user data |
| Identity | [`user_profiles`](#user_profiles) | 6 | 1:1 contact/address details |
| Identity | [`user_preferences`](#user_preferences) | 6 | 1:1 settings |
| Identity | [`sessions`](#sessions) | 0 | Access/refresh tokens |
| Accounts | [`accounts`](#accounts) | 27 | Investment accounts |
| Accounts | [`holdings`](#holdings) | 81 | Positions per account |
| Accounts | [`transactions`](#transactions) | 216 | Account activity |
| Accounts | [`account_performance`](#account_performance) | 27 | 1:1 return summary |
| Accounts | [`performance_points`](#performance_points) | 189 | Chart series |
| Documents | [`documents`](#documents) | 42 | Statements, tax slips, etc. |
| Documents | [`document_shares`](#document_shares) | 0 | Audit of external shares |
| Dashboard | [`goals`](#goals) | 18 | Savings goals |
| Dashboard | [`portfolio_snapshots`](#portfolio_snapshots) | 6 | 1:1 day change |
| Dashboard | [`asset_allocations`](#asset_allocations) | 19 | Allocation breakdown |
| Dashboard | [`alerts`](#alerts) | 6 | Dashboard banners |
| Dashboard | [`quick_links`](#quick_links) | 11 | Dashboard shortcuts |
| Notifications | [`notifications`](#notifications) | 16 | Notification centre |
| Learning | [`education_content`](#education_content) | 6 | Course catalogue (EN/FR) |
| Learning | [`education_progress`](#education_progress) | 14 | Per-user progress |
| Learning | [`glossary_terms`](#glossary_terms) | 7 | Glossary (EN/FR) |
| Advisor | [`advisors`](#advisors) | 1 | Advisors |
| Advisor | [`appointments`](#appointments) | 1 | Meetings |
| Advisor | [`advisor_messages`](#advisor_messages) | 13 | Secure messages |
| Market | [`securities`](#securities) | 6 | Quotes |
| Market | [`price_history`](#price_history) | 408 | Chart prices |
| Market | [`watchlist`](#watchlist) | 30 | Per-user watchlist |
| Market | [`market_news`](#market_news) | 3 | Headlines |
| Market | [`market_roundup_items`](#market_roundup_items) | 3 | Weekly roundup |
| Market | [`baskets`](#baskets) | 4 | Thematic baskets |
| Enrolment | [`offline_clients`](#offline_clients) | 8 | Branch client records |
| Enrolment | [`security_questions`](#security_questions) | 5 | Question list (EN/FR) |
| Enrolment | [`registrations`](#registrations) | 0 | In-progress sign-ups |
| Enrolment | [`registration_security_answers`](#registration_security_answers) | 0 | Answers during sign-up |
| Enrolment | [`user_security_answers`](#user_security_answers) | 0 | Answers after sign-up |
| Infra | [`schema_migrations`](#schema_migrations) | 7 | Applied migrations |

---

## Identity and Auth

### users
Online-banking login for a client. Deleting a user cascades to 16 tables but is **blocked** while `offline_clients.enrolled_user_id` or `registrations.user_id` references it.

| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `id` | TEXT | N | PK | | User id (`sarah`, `u-<8 hex>`) |
| `email` | TEXT | N | UQ | | Login email; `COLLATE NOCASE` |
| `password` | TEXT | N | | | **Demo only, plain text** |
| `mfa_code` | TEXT | N | | | **Demo only**, fixed one-time code |
| `name` | TEXT | N | | | Display name |
| `account_number` | TEXT | N | | | Branch client number (matches `offline_clients`, no FK) |
| `date_of_birth` | TEXT | N | | | `YYYY-MM-DD` |
| `created_at` | TEXT | N | | `strftime('%Y-%m-%dT%H:%M:%SZ','now')` | Created (UTC) |
| `advisor_id` | TEXT | Y | FK→`advisors.id` (NO ACTION) | NULL | Assigned advisor; NULL = none |
| `mfa_method` | TEXT | N | | `'sms'` | `sms` \| `email` |
| `terms_accepted_at` | TEXT | Y | | NULL | Terms acceptance time |

### user_profiles
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `user_id` | TEXT | N | PK, FK→`users.id` (CASCADE) | | Owner |
| `phone_masked` | TEXT | N | | | Masked phone (`+1 (416) •••-••42`) |
| `sin_masked` | TEXT | Y | | NULL | Masked SIN (`••• ••• 821`) |
| `member_since` | INTEGER | N | | | Year joined |
| `address_line1` | TEXT | N | | | |
| `address_line2` | TEXT | Y | | NULL | |
| `city` | TEXT | N | | | |
| `province` | TEXT | N | | | e.g. `ON` |
| `postal_code` | TEXT | N | | | e.g. `M5V 2A1` |
| `country` | TEXT | N | | | e.g. `Canada` |
| `trusted_contact_name` | TEXT | Y | | NULL | |
| `trusted_contact_relationship` | TEXT | Y | | NULL | |

### user_preferences
No column has a default; inserts must supply all of them.

| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `user_id` | TEXT | N | PK, FK→`users.id` (CASCADE) | | Owner |
| `notify_email` | INTEGER | N | | | 0/1 |
| `notify_sms` | INTEGER | N | | | 0/1 |
| `notify_push` | INTEGER | N | | | 0/1 |
| `document_delivery` | TEXT | N | | | `digital` \| `paper_and_digital` |
| `language` | TEXT | N | | | `en-CA` \| `fr-CA` |
| `theme` | TEXT | N | | | `light` \| `dark` \| `system` |
| `default_meeting_type` | TEXT | N | | | `video` \| `phone` \| `in_person` |

### sessions
Shared by the Spring API and the Node API. Rows are never deleted.

| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `access_token` | TEXT | N | PK | | Bearer token `mock-at-<uuid>` |
| `refresh_token` | TEXT | N | UQ | | `mock-rt-<uuid>` |
| `user_id` | TEXT | N | FK→`users.id` (CASCADE) | | Owner (indexed: `ix_sessions_user`) |
| `mfa_verified` | INTEGER | N | | `0` | 0/1 |
| `revoked` | INTEGER | N | | `0` | 0/1 |
| `expires_at` | TEXT | N | | | Now + 900 s (ms precision) |
| `created_at` | TEXT | N | | | Issue time (ms precision) |

## Accounts and Investments

### accounts
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `user_id` | TEXT | N | PK1, FK→`users.id` (CASCADE) | | Owner |
| `id` | TEXT | N | PK2 | | `tfsa`, `acct-<8 hex>` |
| `type` | TEXT | N | | | `TFSA` \| `RRSP` \| `RESP` \| `RDSP` \| `FHSA` \| `NON_REGISTERED` \| `LIRA` \| `RRIF` |
| `display_name` | TEXT | N | | | |
| `nickname` | TEXT | Y | | NULL | User label |
| `balance` | INTEGER | N | | | Money |
| `currency` | TEXT | N | | | |
| `hidden` | INTEGER | N | | `0` | 0/1; hidden accounts are excluded from lists and the total |
| `day_change` | INTEGER | Y | | NULL | Money |
| `day_change_pct` | REAL | Y | | NULL | Percent |
| `contribution_room` | INTEGER | Y | | NULL | Money; NULL = n/a |
| `tax_status` | TEXT | N | | | `Tax-free` \| `Tax-deferred` \| `Taxable` (not enforced) |
| `rate_of_return` | REAL | Y | | NULL | Percent |
| `sort_order` | INTEGER | N | | `0` | Display order |

### holdings
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `user_id` | TEXT | N | PK1, FK→`accounts(user_id,id)` (CASCADE) | | |
| `account_id` | TEXT | N | PK2, FK (composite) | | |
| `symbol` | TEXT | N | PK3 | | Ticker (no FK to `securities`) |
| `name` | TEXT | N | | | Security name |
| `quantity` | REAL | N | | | Units |
| `market_value` | INTEGER | N | | | Money |
| `currency` | TEXT | N | | | |
| `weight_pct` | REAL | N | | | Percent of the account |

### transactions
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `user_id` | TEXT | N | PK1, FK→`accounts(user_id,id)` (CASCADE) | | |
| `account_id` | TEXT | N | PK2, FK (composite) | | |
| `id` | TEXT | N | PK3 | | Unique per account |
| `date` | TEXT | N | | | UTC timestamp (`2024-12-01T00:00:00Z`) |
| `description` | TEXT | N | | | |
| `detail` | TEXT | Y | | NULL | |
| `type` | TEXT | N | | | `contribution` \| `buy` \| `sell` \| `dividend` \| `fee` \| `withdrawal` \| `transfer` \| `scheduled` |
| `amount` | INTEGER | N | | | Money, signed (negative = out) |
| `currency` | TEXT | N | | | |
| `status` | TEXT | N | | `'posted'` | `posted` \| `scheduled` |

Index: `ix_transactions_account_date (user_id, account_id, date DESC)`.

### account_performance
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `user_id` | TEXT | N | PK1, FK→`accounts(user_id,id)` (CASCADE) | | |
| `account_id` | TEXT | N | PK2, FK (composite) | | |
| `time_weighted_return_pct` | REAL | N | | | Percent |
| `currency` | TEXT | N | | | |
| `benchmark_name` | TEXT | N | | | e.g. `S&P/TSX Composite` |

### performance_points
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `user_id` | TEXT | N | PK1, FK→`accounts(user_id,id)` (CASCADE) | | |
| `account_id` | TEXT | N | PK2, FK (composite) | | |
| `series` | TEXT | N | PK3 | | `account` (value = money) \| `benchmark` (value = index, start 100) |
| `period` | TEXT | N | PK4 | | `YYYY-MM` |
| `value` | REAL | N | | | See `series` |

## Documents

### documents
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `user_id` | TEXT | N | PK1, FK→`users.id` (CASCADE) | | |
| `id` | TEXT | N | PK2 | | `d1` … |
| `name` | TEXT | N | | | |
| `category` | TEXT | N | | | `Tax` \| `Statements` \| `Confirmations` \| `Legal` (not enforced) |
| `year` | INTEGER | N | | | |
| `account_id` | TEXT | Y | | NULL | Related account (**no FK**); NULL = all accounts |
| `account_label` | TEXT | Y | | NULL | Denormalised account name |
| `size_bytes` | INTEGER | N | | | |
| `action_required` | INTEGER | N | | `0` | 0/1 |
| `url` | TEXT | N | | | `mock://signed/<id>` |

### document_shares
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `id` | INTEGER | N | PK AUTOINCREMENT | auto | Surrogate id |
| `user_id` | TEXT | N | FK→`documents(user_id,id)` (CASCADE) | | |
| `document_id` | TEXT | N | FK (composite) | | |
| `recipient` | TEXT | N | | | Recipient email |
| `created_at` | TEXT | N | | | |
| `expires_at` | TEXT | N | | | Created + 7 days |

## Goals and Dashboard

### goals
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `user_id` | TEXT | N | PK1, FK→`users.id` (CASCADE) | | |
| `id` | TEXT | N | PK2 | | |
| `name` | TEXT | N | | | |
| `category` | TEXT | N | | | `Education` \| `Vacation` \| `Home` \| `Retirement` \| `Safety` |
| `current_amount` | INTEGER | N | | | Money |
| `target_amount` | INTEGER | N | | | Money |
| `currency` | TEXT | N | | | |
| `target_date` | TEXT | N | | | `YYYY-MM-DD` |
| `progress_pct` | REAL | N | | | Stored percent |
| `show_on_dashboard` | INTEGER | N | | `0` | 0/1 |
| `sort_order` | INTEGER | N | | `0` | |

### portfolio_snapshots
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `user_id` | TEXT | N | PK, FK→`users.id` (CASCADE) | | |
| `currency` | TEXT | N | | | |
| `day_change` | INTEGER | N | | | Money |
| `day_change_pct` | REAL | N | | | Percent |
| `as_of` | TEXT | N | | | UTC |

Total portfolio value is **not stored**. It is `SUM(accounts.balance) WHERE hidden = 0`.

### asset_allocations
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `user_id` | TEXT | N | PK1, FK→`users.id` (CASCADE) | | |
| `asset_class` | TEXT | N | PK2 | | `Equity`, `Fixed Income`, `Cash`, `Alternatives` |
| `pct` | REAL | N | | | Percent |
| `sort_order` | INTEGER | N | | `0` | |

### alerts
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `user_id` | TEXT | N | PK1, FK→`users.id` (CASCADE) | | |
| `id` | TEXT | N | PK2 | | |
| `type` | TEXT | N | | | `ACTION_REQUIRED` \| `INFO` \| `SUCCESS` \| `WARNING` |
| `title` | TEXT | N | | | |
| `deep_link` | TEXT | Y | | NULL | `electricwealth://…` |

### quick_links
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `user_id` | TEXT | N | PK1, FK→`users.id` (CASCADE) | | |
| `sort_order` | INTEGER | N | PK2 | | Position |
| `label` | TEXT | N | | | English label (localised by the API) |
| `deep_link` | TEXT | N | | | |

## Notifications

### notifications
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `user_id` | TEXT | N | PK1, FK→`users.id` (CASCADE) | | |
| `id` | TEXT | N | PK2 | | |
| `title` | TEXT | N | | | |
| `body` | TEXT | N | | | |
| `type` | TEXT | N | | | `ACTION_REQUIRED` \| `INFO` \| `SUCCESS` \| `WARNING` |
| `created_at` | TEXT | N | | | UTC |
| `read` | INTEGER | N | | `0` | 0/1 |

Index: `ix_notifications_user_created (user_id, created_at DESC)`.

## Learning Centre

### education_content
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `id` | TEXT | N | PK | | `e1` … |
| `title_en` | TEXT | N | | | |
| `title_fr` | TEXT | N | | | |
| `level` | TEXT | N | | | `beginner` \| `intermediate` \| `advanced` |
| `type` | TEXT | N | | | `class` \| `challenge` |
| `duration_min` | INTEGER | N | | | Minutes, `> 0` |
| `sort_order` | INTEGER | N | | `0` | |

### education_progress
No row means 0%.

| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `user_id` | TEXT | N | PK1, FK→`users.id` (CASCADE) | | |
| `content_id` | TEXT | N | PK2, FK→`education_content.id` (CASCADE) | | |
| `progress_pct` | INTEGER | N | | | 0–100 |
| `updated_at` | TEXT | N | | | UTC |

### glossary_terms
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `id` | TEXT | N | PK | | Slug |
| `term_en` | TEXT | N | | | |
| `term_fr` | TEXT | N | | | |
| `definition_en` | TEXT | N | | | |
| `definition_fr` | TEXT | N | | | |
| `sort_order` | INTEGER | N | | `0` | |

## Advisor

### advisors
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `id` | TEXT | N | PK | | `adv-1` |
| `name` | TEXT | N | | | |
| `designation` | TEXT | Y | | NULL | e.g. `CFP` |
| `available` | INTEGER | N | | `1` | 0/1; 0 = no bookable slots |

### appointments
`date` and `time` are **Toronto local time**. Only one `scheduled` meeting is allowed per advisor slot (partial unique index `ux_appointments_advisor_slot`).

| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `id` | TEXT | N | PK | | `appt-<8 hex>` |
| `user_id` | TEXT | N | FK→`users.id` (CASCADE) | | Client |
| `advisor_id` | TEXT | N | FK→`advisors.id` (NO ACTION) | | |
| `meeting_type` | TEXT | N | | | `video` \| `phone` \| `in_person` |
| `date` | TEXT | N | | | `YYYY-MM-DD` (Toronto) |
| `time` | TEXT | N | | | `HH:MM` (Toronto) |
| `status` | TEXT | N | | `'scheduled'` | `scheduled` \| `completed` \| `cancelled` |
| `notes` | TEXT | Y | | NULL | |
| `created_at` | TEXT | N | | | UTC |

### advisor_messages
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `id` | TEXT | N | PK | | `msg-<8 hex>` |
| `user_id` | TEXT | N | FK→`users.id` (CASCADE) | | Client |
| `advisor_id` | TEXT | N | FK→`advisors.id` (NO ACTION) | | |
| `sender` | TEXT | N | | | `advisor` \| `client` |
| `text` | TEXT | N | | | |
| `sent_at` | TEXT | N | | | UTC |

## Market Data (static demo data)

### securities
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `symbol` | TEXT | N | PK | | `XEQT.TO`, `AAPL` |
| `name` | TEXT | N | | | |
| `exchange` | TEXT | N | | | `TSX`, `NASDAQ` |
| `currency` | TEXT | N | | | Trading currency |
| `price` | INTEGER | N | | | Money (last price) |
| `change` | INTEGER | N | | | Money (vs previous close) |
| `change_pct` | REAL | N | | | Percent |
| `about` | TEXT | Y | | NULL | |

### price_history
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `symbol` | TEXT | N | PK1, FK→`securities.symbol` (CASCADE) | | |
| `range` | TEXT | N | PK2 | | `1D` \| `1M` \| `1Y` \| `5Y` |
| `t` | TEXT | N | PK3 | | `HH:MM` (1D), `YYYY-MM-DD` (1M), `YYYY-MM` (1Y/5Y) |
| `close` | INTEGER | N | | | Money |

### watchlist
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `user_id` | TEXT | N | PK1, FK→`users.id` (CASCADE) | | |
| `symbol` | TEXT | N | PK2, FK→`securities.symbol` (CASCADE) | | |
| `added_at` | TEXT | N | | | UTC |

### market_news
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `id` | TEXT | N | PK | | |
| `title` | TEXT | N | | | |
| `source` | TEXT | N | | | Publisher |
| `published_at` | TEXT | N | | | UTC |
| `tag` | TEXT | Y | | NULL | Topic |

### market_roundup_items
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `id` | TEXT | N | PK | | |
| `week_of` | TEXT | N | | | `YYYY-MM-DD` of the covered week |
| `title` | TEXT | N | | | |
| `summary` | TEXT | N | | | |
| `published_at` | TEXT | N | | | UTC |

### baskets
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `id` | TEXT | N | PK | | |
| `name` | TEXT | N | | | |
| `description` | TEXT | N | | | |
| `return_1y_pct` | REAL | N | | | Percent |
| `holdings_count` | INTEGER | N | | | |
| `risk` | TEXT | N | | | `Low` \| `Medium` \| `High` |
| `sort_order` | INTEGER | N | | `0` | |

## Online Enrolment

### offline_clients
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `account_number` | TEXT | N | PK | | Branch client number |
| `name` | TEXT | N | | | |
| `date_of_birth` | TEXT | N | | | `YYYY-MM-DD` (identity factor) |
| `phone_masked` | TEXT | N | | | |
| `sin_masked` | TEXT | Y | | NULL | |
| `client_since` | INTEGER | N | | | Year |
| `address_line1` | TEXT | N | | | |
| `address_line2` | TEXT | Y | | NULL | |
| `city` | TEXT | N | | | |
| `province` | TEXT | N | | | |
| `postal_code` | TEXT | N | | | `A1A 1A1` (identity factor) |
| `country` | TEXT | N | | | |
| `language` | TEXT | N | | | `en-CA` \| `fr-CA` |
| `advisor_id` | TEXT | Y | FK→`advisors.id` (NO ACTION) | NULL | |
| `enrolled_user_id` | TEXT | Y | FK→`users.id` (NO ACTION) | NULL | Set when enrolled online |

### security_questions
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `id` | TEXT | N | PK | | `q1` … `q5` |
| `text_en` | TEXT | N | | | |
| `text_fr` | TEXT | N | | | |
| `sort_order` | INTEGER | N | | `0` | |

### registrations
Open = `user_id IS NULL AND expires_at > now`.

| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `id` | TEXT | N | PK | | `reg-<8 hex>` |
| `email` | TEXT | N | | | `COLLATE NOCASE`, **not unique** |
| `password` | TEXT | N | | | **Demo only, plain text** |
| `account_number` | TEXT | Y | FK→`offline_clients.account_number` (NO ACTION) | NULL | Set when identity is verified |
| `trusted_contact_name` | TEXT | Y | | NULL | |
| `trusted_contact_relationship` | TEXT | Y | | NULL | |
| `trusted_contact_phone` | TEXT | Y | | NULL | |
| `trusted_contact_email` | TEXT | Y | | NULL | |
| `security_answers_saved` | INTEGER | N | | `0` | 0/1 |
| `mfa_method` | TEXT | Y | | NULL | `sms` \| `email` |
| `consented_at` | TEXT | Y | | NULL | |
| `user_id` | TEXT | Y | FK→`users.id` (NO ACTION) | NULL | Set on completion |
| `created_at` | TEXT | N | | | |
| `expires_at` | TEXT | N | | | Created + 30 min |

### registration_security_answers
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `registration_id` | TEXT | N | PK1, FK→`registrations.id` (CASCADE) | | |
| `question_id` | TEXT | N | PK2, FK→`security_questions.id` (NO ACTION) | | |
| `answer` | TEXT | N | | | Normalised (trimmed, lower-case) |

### user_security_answers
| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `user_id` | TEXT | N | PK1, FK→`users.id` (CASCADE) | | |
| `question_id` | TEXT | N | PK2, FK→`security_questions.id` (NO ACTION) | | |
| `answer` | TEXT | N | | | Normalised |

## Infrastructure

### schema_migrations
Created by the migration runner (`scripts/lib.js`).

| Column | Type | Null | Key | Default | Description |
|---|---|---|---|---|---|
| `version` | TEXT | N | PK | | Migration file name |
| `applied_at` | TEXT | N | | `strftime('%Y-%m-%dT%H:%M:%SZ','now')` | |
