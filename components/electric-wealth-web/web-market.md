---
component_id: web-market
repo: web
type: ui-feature
description: >-
  Use when the user searches securities, manages the watchlist, reads news and the weekly roundup, views a security's price chart, or browses thematic baskets at /market, /market/:symbol or /market/baskets.
triggers:
  - "market page"
  - "watchlist"
  - "security search"
  - "security page"
  - "price chart"
  - "news"
  - "weekly roundup"
  - "baskets page"
paths:
  - "src/features/market/**"
depends_on:
  - "api-market"
  - "web-api-client"
  - "web-design-system"
  - "web-i18n-and-formatting"
consumed_by: []
data_entities:
  - "Security"
  - "PriceHistory"
  - "Watchlist"
  - "MarketNews"
  - "MarketRoundupItem"
  - "Basket"
last_verified_against_commit: 7489670e43b9b3498218e9a1559254ff6a581ea3
last_feature_id: ""
source_doc: docs/web-app-internal/13-feature-modules.md
verification_signal: route-inventory-diff
---

# Market Pages

> **Repo:** `web` ([repo summary](../../repos/electric-wealth-web/README.md)) · **Type:** `ui-feature` · **Source doc:** [`docs/web-app-internal/13-feature-modules.md`](../../../electric-wealth-web/docs/web-app-internal/13-feature-modules.md)

## 1. Overview

`MarketPage`, `SecurityPage` and `BasketsPage`. Search is deferred and fires at two or more characters; the security chart supports `1D` / `1M` / `1Y` / `5Y` ranges and derives range change, high and low client-side.

**Routes:** `/market`, `/market/:symbol`, `/market/baskets`

**Depends on:** [`api-market`](../../components/electric-wealth-api-spring/api-market.md), [`web-api-client`](../../components/electric-wealth-web/web-api-client.md), [`web-design-system`](../../components/electric-wealth-web/web-design-system.md), [`web-i18n-and-formatting`](../../components/electric-wealth-web/web-i18n-and-formatting.md)

**Shared entities:** [`Security`](../../shared/entities/security.md), [`PriceHistory`](../../shared/entities/price-history.md), [`Watchlist`](../../shared/entities/watchlist.md), [`MarketNews`](../../shared/entities/market-news.md), [`MarketRoundupItem`](../../shared/entities/market-roundup-item.md), [`Basket`](../../shared/entities/basket.md)

---

## 2. Key Responsibilities

- Search securities.
- Show and edit the watchlist.
- Show news, roundup and baskets.
- Show security detail and price history.

**Explicitly NOT in scope:**

- Held/watched flags and market data — owned by [`api-market`](../../components/electric-wealth-api-spring/api-market.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Routes `/market`, `/market/:symbol`, `/market/baskets` | Bookmarks, `returnTo` redirects, dashboard deep-link mapping, navigation in `web-app-shell` | `route-inventory-diff` against `src/App.tsx`, then `npm run verify` |
| Internal refactor (no interface change) | This feature folder only | `npm run typecheck` (no automated UI tests exist) |
| Data-entity schema change | API schema change in `api-market` surfaces as type errors here | `npm run gen:api` then `npm run verify` |
| Dependency upgrade | Whole SPA bundle | `npm run build` |

**Upstream dependents to notify:** None modelled.

**Downstream services to monitor post-change:** `api-market`, `web-api-client`, `web-design-system`, `web-i18n-and-formatting`

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `7489670e43b9b3498218e9a1559254ff6a581ea3` (2026-10-19)

**Open follow-ups from extraction:**

- `GET /api/market/quote` is not used by the web app (Part A § 8.2).
