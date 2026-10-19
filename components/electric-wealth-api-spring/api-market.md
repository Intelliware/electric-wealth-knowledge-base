---
component_id: api-market
repo: api-spring
type: api-feature
description: >-
  Use when a client reads quotes, security detail, price history, search results, news, the weekly roundup or thematic baskets, or adds or removes watchlist symbols through /api/market.
triggers:
  - "market"
  - "quote"
  - "security detail"
  - "price history"
  - "search securities"
  - "news"
  - "weekly roundup"
  - "baskets"
  - "watchlist"
  - "held flag"
paths:
  - "src/main/java/com/electricwealth/api/web/MarketController.java"
  - "src/main/java/com/electricwealth/api/dto/MarketDtos.java"
  - "src/main/java/com/electricwealth/api/repository/MarketRepository.java"
  - "src/test/java/com/electricwealth/api/EducationAndMarketApiTest.java"
depends_on:
  - "db-market-data"
  - "db-accounts-and-investments"
  - "api-request-pipeline"
  - "api-contract-and-data-access"
consumed_by:
  - "web-market"
data_entities:
  - "Security"
  - "PriceHistory"
  - "Watchlist"
  - "MarketNews"
  - "MarketRoundupItem"
  - "Basket"
  - "Holding"
last_verified_against_commit: 3f7403e33d97b39872e89690431c7880dea98ae4
last_feature_id: ""
source_doc: docs/api-internal/12-domain-models-and-business-rules.md
verification_signal: openapi-diff
---

# Market Data and Watchlist

> **Repo:** `api-spring` ([repo summary](../../repos/electric-wealth-api-spring/README.md)) · **Type:** `api-feature` · **Source doc:** [`docs/api-internal/12-domain-models-and-business-rules.md`](../../../electric-wealth-api-spring/docs/api-internal/12-domain-models-and-business-rules.md)

## 1. Overview

Serves static demo market data. Each quote carries `held` (symbol in a visible account of the user) and `watched` flags. Watchlist add is idempotent (`ON CONFLICT DO NOTHING`), remove of an absent symbol is a no-op; the roundup returns the latest `week_of`.

**Operations:** `GET /api/market/quote`, `GET /api/market/securities/{symbol}`, `GET /api/market/history`, `GET /api/market/search`, `GET /api/market/news`, `GET /api/market/roundup`, `GET /api/market/baskets`, `GET /api/market/watchlist`, `PUT /api/market/watchlist/{symbol}`, `DELETE /api/market/watchlist/{symbol}`

**Depends on:** [`db-market-data`](../../components/electric-wealth-db/db-market-data.md), [`db-accounts-and-investments`](../../components/electric-wealth-db/db-accounts-and-investments.md), [`api-request-pipeline`](../../components/electric-wealth-api-spring/api-request-pipeline.md), [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md)

**Consumed by:** [`web-market`](../../components/electric-wealth-web/web-market.md)

**Shared entities:** [`Security`](../../shared/entities/security.md), [`PriceHistory`](../../shared/entities/price-history.md), [`Watchlist`](../../shared/entities/watchlist.md), [`MarketNews`](../../shared/entities/market-news.md), [`MarketRoundupItem`](../../shared/entities/market-roundup-item.md), [`Basket`](../../shared/entities/basket.md), [`Holding`](../../shared/entities/holding.md)

---

## 2. Key Responsibilities

- Serve quotes, security detail, history and search.
- Serve news, roundup and baskets.
- Maintain the user's watchlist.

**Explicitly NOT in scope:**

- Account holdings and balances — owned by [`api-accounts`](../../components/electric-wealth-api-spring/api-accounts.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| HTTP operations (10) in `openapi.json`, tag `Market` | Web callers `web-market`; web `src/api/schema.d.ts` must be regenerated; Node API parity | `openapi-diff`: `OpenApiSpecTest`, `ContractParityTest`, then `npm run verify` in `electric-wealth-web` |
| Internal refactor (no interface change) | This component's controller / repository only | `./mvnw test -Dtest=EducationAndMarketApiTest` |
| Data-entity schema change | Schema owner(s) `db-market-data`, `db-accounts-and-investments` and every API feature reading the same tables | `schema-diff` on `electric-wealth-db` + full `./mvnw test` |
| Dependency upgrade | Whole API (single Maven module) | `./mvnw verify` incl. `OpenApiSpecTest` |

**Upstream dependents to notify:** `web-market`

**Downstream services to monitor post-change:** `db-market-data`, `db-accounts-and-investments`, `api-request-pipeline`, `api-contract-and-data-access`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `3f7403e33d97b39872e89690431c7880dea98ae4` (2026-10-19)

**Open follow-ups from extraction:**

- None recorded at initial build.
