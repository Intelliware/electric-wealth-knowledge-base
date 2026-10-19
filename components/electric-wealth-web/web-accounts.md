---
component_id: web-accounts
repo: web
type: ui-feature
description: >-
  Use when the user views the accounts list, an account's detail (performance vs benchmark, holdings, transactions, nickname) or opens a new account at /accounts, /accounts/:id or /accounts/new.
triggers:
  - "accounts page"
  - "account detail"
  - "open an account"
  - "holdings table"
  - "transactions list"
  - "performance chart"
  - "nickname editor"
  - "hide balances"
paths:
  - "src/features/accounts/**"
depends_on:
  - "api-accounts"
  - "web-api-client"
  - "web-design-system"
  - "web-i18n-and-formatting"
consumed_by: []
data_entities:
  - "Account"
  - "Holding"
  - "Transaction"
  - "AccountPerformance"
last_verified_against_commit: 7489670e43b9b3498218e9a1559254ff6a581ea3
last_feature_id: ""
source_doc: docs/web-app-internal/13-feature-modules.md
verification_signal: route-inventory-diff
---

# Accounts Pages

> **Repo:** `web` ([repo summary](../../repos/electric-wealth-web/README.md)) · **Type:** `ui-feature` · **Source doc:** [`docs/web-app-internal/13-feature-modules.md`](../../../electric-wealth-web/docs/web-app-internal/13-feature-modules.md)

## 1. Overview

`AccountsPage`, `AccountDetailPage` and the three-step `AccountOpenPage`. Detail combines `useAccount`, `usePerformance(range)` and `useTransactions(page, type)` (10 per page, filter all / contribution / buy / dividend); the balance-hiding toggle is local only.

**Routes:** `/accounts`, `/accounts/new`, `/accounts/:id`

**Depends on:** [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md), [`web-api-client`](../../components/electric-wealth-web/web-api-client.md), [`web-design-system`](../../components/electric-wealth-web/web-design-system.md), [`web-i18n-and-formatting`](../../components/electric-wealth-web/web-i18n-and-formatting.md)

**Shared entities:** [`Account`](../../shared/entities/account.md), [`Holding`](../../shared/entities/holding.md), [`Transaction`](../../shared/entities/transaction.md), [`AccountPerformance`](../../shared/entities/account-performance.md)

---

## 2. Key Responsibilities

- List accounts with totals.
- Show account detail, performance, holdings and transactions.
- Rename an account.
- Open a new account.

**Explicitly NOT in scope:**

- Account rules and defaults — owned by [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Routes `/accounts`, `/accounts/new`, `/accounts/:id` | Bookmarks, `returnTo` redirects, dashboard deep-link mapping, navigation in `web-app-shell` | `route-inventory-diff` against `src/App.tsx`, then `npm run verify` |
| Internal refactor (no interface change) | This feature folder only | `npm run typecheck` (no automated UI tests exist) |
| Data-entity schema change | API schema change in `api-accounts` surfaces as type errors here | `npm run gen:api` then `npm run verify` |
| Dependency upgrade | Whole SPA bundle | `npm run build` |

**Upstream dependents to notify:** None modelled.

**Downstream services to monitor post-change:** `api-accounts`, `web-api-client`, `web-design-system`, `web-i18n-and-formatting`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `7489670e43b9b3498218e9a1559254ff6a581ea3` (2026-10-19)

**Open follow-ups from extraction:**

- `useAccounts()` only fetches page 1 (`pageSize: 20`) — users with more accounts would not see them all (Part A § 13.3).
