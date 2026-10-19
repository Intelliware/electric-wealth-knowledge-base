---
component_id: db-market-data
repo: db
type: database-schema
description: >-
  Use when a change touches static market data or watchlists — securities, price history, news, weekly roundup items, thematic baskets or the per-user watchlist table.
triggers:
  - "securities table"
  - "price history"
  - "watchlist"
  - "market news"
  - "market roundup"
  - "baskets"
  - "static demo data"
paths:
  - "migrations/006_market.sql"
  - "seed/106_market.sql"
depends_on:
  - "db-identity-and-auth"
consumed_by:
  - "api-market"
data_entities:
  - "Security"
  - "PriceHistory"
  - "Watchlist"
  - "MarketNews"
  - "MarketRoundupItem"
  - "Basket"
last_verified_against_commit: e6262fcddca7f4c0488248ca8881483d9b920f0a
last_feature_id: ""
source_doc: docs/database-internal/05-tables-detailed-documentation.md
verification_signal: schema-diff
---

# Market Data Schema

> **Repo:** `db` ([repo summary](../../repos/electric-wealth-db/README.md)) · **Type:** `database-schema` · **Source doc:** [`docs/database-internal/05-tables-detailed-documentation.md`](../../../electric-wealth-db/docs/database-internal/05-tables-detailed-documentation.md)

## 1. Overview

Introduced by migration 006. Everything except `watchlist` is static demo data (no feed). `price_history` and `watchlist` reference `securities.symbol`; `market_news`, `market_roundup_items` and `baskets` are standalone.

`price_history` is the largest table in the seed (408 rows). `watchlist` is the only table here written at runtime (`api-market` add/remove).

**Tables:** `securities`, `price_history`, `watchlist`, `market_news`, `market_roundup_items`, `baskets`

**Depends on:** [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md)

**Consumed by:** [`api-market`](../../components/electric-wealth-api-spring/api-market.md)

**Shared entities:** [`Security`](../../shared/entities/security.md), [`PriceHistory`](../../shared/entities/price-history.md), [`Watchlist`](../../shared/entities/watchlist.md), [`MarketNews`](../../shared/entities/market-news.md), [`MarketRoundupItem`](../../shared/entities/market-roundup-item.md), [`Basket`](../../shared/entities/basket.md)

---

## 2. Key Responsibilities

- Define security master data and closing-price history.
- Store per-user watchlists ordered by `added_at`.
- Hold news, weekly roundup and basket reference content.

**Explicitly NOT in scope:**

- Held/watched flags and range filtering — owned by [`api-market`](../../components/electric-wealth-api-spring/api-market.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Tables `securities`, `price_history`, `watchlist`, `market_news`, `market_roundup_items`, `baskets` | Direct SQL readers `api-market`; Node API (`electric-wealth-api`) shares the same file | `schema-diff` (data dictionary vs `pragma_table_info`), then `./mvnw test` in `electric-wealth-api-spring` |
| Internal refactor (no interface change) | Seed data / indexes only | `npm run db:reset` then `npm run db:status` |
| Data-entity schema change | New migration required; every API repository touching these tables | Part A § 20 new-table checklist + API test suite |
| Dependency upgrade | Node `node:sqlite` / SQLite version affects all schema components | `npm run db:reset` + API test suite |

**Upstream dependents to notify:** `api-market` Also the Node API (`electric-wealth-api`, not in this workspace), which shares the database file.

**Downstream services to monitor post-change:** `db-identity-and-auth`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `e6262fcddca7f4c0488248ca8881483d9b920f0a` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
