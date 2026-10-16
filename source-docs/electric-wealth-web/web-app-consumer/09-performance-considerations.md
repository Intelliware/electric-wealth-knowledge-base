---
title: "Performance Considerations"
block: reverse-eng/web-app/consumer/performance-considerations
part: B
source_repo: electric-wealth-web
source_version: 0.1.0
source_commit: bbb4907dd0d866223742c529ea65a75ec8af576f
extracted: 2026-10-16
---

## 9. Performance Considerations

### 9.1 Load Cost Per Mode

One mode (standalone). Production build of `bbb4907` (Vite 8.3.3, 2026-10-16):

| Asset | Size | Gzip |
|---|---|---|
| HTML | 0.52 kB | 0.32 kB |
| CSS (1 file) | 22.59 kB | 5.12 kB |
| JavaScript (1 file, whole app incl. both languages) | 466.15 kB | 140.42 kB |

No web fonts, images (except an SVG favicon) or third-party scripts are loaded.

### 9.2 Lazy Loading and Deferred Work

None at the bundle level: every page is in the single JavaScript file. At the data level, each page fetches only its
own data when opened; search fields wait for at least 2 characters (market) and defer requests while the user is
typing (market, glossary).

### 9.3 Caching and CDN

No CDN or cache headers are configured (no hosting config exists). Asset file names contain a content hash, so
`assets/*` can be cached long-term; `index.html` must not be cached long-term. In the browser, API responses are cached
in memory for 30 seconds (profile and preferences 5 minutes) and are not re-fetched when the window regains focus.

### 9.4 Runtime Cost on the Host Page

Not applicable (no host page).

### 9.5 Network and API Cost

Per signed-in tab, in the background:

| Activity | Frequency |
|---|---|
| Notifications refresh (`GET /api/notifications`) | Every 60 s |
| Token refresh (`POST /api/auth/refresh`) | Every 10 min |

Signing in loads the profile, preferences and notifications once for the page frame; each page then adds 1–3 calls
(e.g. dashboard 1, account detail 3, market 3). Failed calls are retried once only for network errors and 5xx; 4xx
(including `429`) are not retried.

**Rate limit:** the API allows **300 requests per minute per client IP** across all endpoints and returns
`429 RATE_LIMITED` above that. Because the web app must reach the API through the same origin, a reverse proxy in
front of the API will make all users appear as the proxy's IP unless the API is changed to trust forwarded client
addresses — at which point a few hundred active tabs could exhaust a shared budget. See the API consumer guide
`10-rate-limits.md`.

### 9.6 Multiple Instances on One Page

Not applicable. Several tabs of the app are independent: each has its own session, cache and background polling.

### 9.7 Performance Budgets and Telemetry

None. No performance budget, no Web Vitals reporting, no real-user monitoring.

### 9.8 Performance Anti-Patterns

- Leaving many tabs signed in multiplies background polling against the shared rate limit.
- Serving `index.html` with long-term cache headers would pin users to an old bundle.
- Documents and goals are fetched up to 100 items at once with no paging; very large lists will load slowly.

