# 05 — Data Dictionary (Aggregated)

Canonical home: [`shared/entities/`](../../shared/entities/). Full column-level dictionary: [`source-docs/electric-wealth-db/data-dictionary.md`](../../source-docs/electric-wealth-db/data-dictionary.md); ERD: [`erd.mmd`](../../source-docs/electric-wealth-db/erd.mmd).

| Entity | Summary | Owning component |
|---|---|---|
| [`account`](../../shared/entities/account.md) | An investment account (TFSA, RRSP, FHSA, non-registered, …) owned by a user, with balance, day change, tax status, contribution room, nickname and visibility. | `api-accounts` |
| [`account-performance`](../../shared/entities/account-performance.md) | An account's time-weighted return and its performance series against a benchmark (header row plus points). | `api-accounts` |
| [`advisor`](../../shared/entities/advisor.md) | A financial advisor (name, designation, availability) assigned to users and branch clients. | `api-advisor` |
| [`advisor-message`](../../shared/entities/advisor-message.md) | One message in the secure thread between a user and their advisor. | `api-advisor` |
| [`alert`](../../shared/entities/alert.md) | An action-required or informational dashboard alert carrying a mobile deep link. | `api-dashboard` |
| [`appointment`](../../shared/entities/appointment.md) | A meeting between a user and their advisor in a fixed slot (Toronto local date and time) with meeting type, notes and status. | `api-advisor` |
| [`asset-allocation`](../../shared/entities/asset-allocation.md) | One slice of the user's asset-allocation bar (asset class and weight). | `api-dashboard` |
| [`basket`](../../shared/entities/basket.md) | A thematic basket of securities with risk level, description, 1-year return and holdings count. | `api-market` |
| [`document`](../../shared/entities/document.md) | Metadata for a client document (statement, tax slip, confirmation, legal) with year, category, size and action-required flag. No binary is stored. | `api-documents` |
| [`document-share`](../../shared/entities/document-share.md) | A record that a document was shared with an email recipient, expiring after 7 days. | `api-documents` |
| [`education-content`](../../shared/entities/education-content.md) | A lesson, class or challenge in the learning centre with bilingual title, level, type and duration. | `api-education` |
| [`education-progress`](../../shared/entities/education-progress.md) | A user's completion percentage (0–100) for one content item. | `api-education` |
| [`glossary-term`](../../shared/entities/glossary-term.md) | A bilingual financial glossary term and definition. | `api-education` |
| [`goal`](../../shared/entities/goal.md) | A savings goal with name, category, current and target amounts, target date and whether it appears on the dashboard. | `api-goals` |
| [`holding`](../../shared/entities/holding.md) | A position in a security inside an account: symbol, name, quantity, market value and portfolio weight. | `api-accounts` |
| [`market-news`](../../shared/entities/market-news.md) | A market headline with source and publish time. | `api-market` |
| [`market-roundup-item`](../../shared/entities/market-roundup-item.md) | One item in the weekly market roundup, grouped by `week_of`. | `api-market` |
| [`notification`](../../shared/entities/notification.md) | An in-app message to the user with type (`INFO`, `SUCCESS`, `WARNING`, `ACTION_REQUIRED`), title, body and read flag. | `api-notifications` |
| [`offline-client`](../../shared/entities/offline-client.md) | A branch (offline) client record used to verify identity during enrolment: account number, postal code, date of birth, name, contact details, language and advisor. | `api-registration` |
| [`portfolio-snapshot`](../../shared/entities/portfolio-snapshot.md) | A user's dashboard snapshot: currency, day change and as-of time. Total value is deliberately not stored. | `api-dashboard` |
| [`price-history`](../../shared/entities/price-history.md) | Chart price points for a security per range: intraday times for `1D`, days for `1M`, months for `1Y` / `5Y`. | `api-market` |
| [`quick-link`](../../shared/entities/quick-link.md) | An ordered dashboard shortcut (label + mobile deep link). | `api-dashboard` |
| [`registration`](../../shared/entities/registration.md) | A draft online enrolment: chosen email and password, the matched branch account, step flags, MFA method, consent time and the user it produced. | `api-registration` |
| [`security`](../../shared/entities/security.md) | A tradable security (symbol, name, exchange, trading currency, last price, change and an "about" text) in the static market dataset. | `api-market` |
| [`security-answer`](../../shared/entities/security-answer.md) | A normalised answer to a security question — staged per registration, then copied to the new user on completion. | `api-registration` |
| [`security-question`](../../shared/entities/security-question.md) | A bilingual security question offered during enrolment. | `api-registration` |
| [`session`](../../shared/entities/session.md) | A bearer session: an opaque access/refresh token pair belonging to a user, with MFA status, revocation flag and expiry. Shared with the Node API. | `api-auth-sessions` |
| [`transaction`](../../shared/entities/transaction.md) | A posted or scheduled money movement in an account (contribution, buy, dividend, …). | `api-accounts` |
| [`user`](../../shared/entities/user.md) | An online-banking login for an investment client: email, demo credentials, display name, branch account number, date of birth, MFA method and assigned advisor. | `api-registration` |
| [`user-preferences`](../../shared/entities/user-preferences.md) | Per-user settings: notification channels (email, SMS, push), document delivery, language, theme and default advisor meeting type. | `api-profile-and-preferences` |
| [`user-profile`](../../shared/entities/user-profile.md) | Personal details shown to the client: masked phone and SIN, member-since year, mailing address and trusted contact. | `api-profile-and-preferences` |
| [`watchlist`](../../shared/entities/watchlist.md) | The ordered set of securities a user watches. | `api-market` |
