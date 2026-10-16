---
title: "Embedding Options"
block: reverse-eng/web-app/consumer/embedding-options
part: B
source_repo: electric-wealth-web
source_version: 0.1.0
source_commit: bbb4907dd0d866223742c529ea65a75ec8af576f
extracted: 2026-10-16
---

## 1. Embedding Options

> **Scope of this guide.** Electric Wealth Web is a standalone, client-side rendered single-page application for retail
> investors. It has **no embedding, SDK, extension, event or theming API for other products**. Each section below
> states what is not supported, with the evidence, and documents the integration surface that does exist: its
> URLs, its dependency on the Electric Wealth API, its session rules, and its deployment shape.
> Internal details: `electric-wealth-web/docs/web-app-internal/`.

### 1.1 Supported Embedding Modes

| Mode | Supported | Evidence / notes |
|---|---|---|
| **Standalone (link-out)** | ✅ Only mode | Users open the app's URL in a browser tab. Other products can link to any page URL (see [1.3](#13-quick-start-per-mode)). |
| **iframe** | ❌ | No frame-aware code, no `postMessage` handling, no embed route or parameters. Tokens are in `sessionStorage`, so a framed copy would need its own sign-in. |
| **Web Component** | ❌ | No custom element is built or published. |
| **JS SDK / overlay** | ❌ | No SDK bundle; the build emits one app bundle (`index.html` + one JS + one CSS file). |
| **Reverse proxy / path mount** | ⚠️ Root only | The app must be served at the **root** of an origin: Vite `base` is the default `/`, routes are absolute (`/accounts`, …), and the API is called at `<origin>/api/*`. Mounting under a sub-path (e.g. `/wealth/`) is not supported without code changes. |

**Deployment shape** (what an operator hosts):

| Piece | Form |
|---|---|
| Web app | Static files from `npm run build` (`dist/`): `index.html`, `assets/index-<hash>.js` (466 kB, 140 kB gzip), `assets/index-<hash>.css` (22.6 kB, 5.1 kB gzip), `favicon.svg` |
| Rendering | Client-side only (CSR); no SSR, no Node server at runtime, no service worker |
| API | Must be reachable on the **same origin** under `/api/*` — the browser calls `window.location.origin + /api/...` and the API base URL is not configurable |
| Routing | History-API routes: the host must rewrite unknown paths to `index.html` |
| Today | No deployment exists in the repo (no Dockerfile, hosting config or CI). Only local dev (`npm run dev`, port 5173, Vite proxy `/api` → `API_URL`, default `http://localhost:4200`) |

### 1.2 Choosing Between Modes

There is one mode: link to the app. Use the URL contract in [1.3](#13-quick-start-per-mode). If a product needs Electric
Wealth data inside its own UI, integrate with the **Electric Wealth API** instead
(`source-docs/electric-wealth-api-spring/api-consumer/`), not with this app.

### 1.3 Quick Start per Mode

**Standalone — linkable URLs.** Any signed-in page can be linked directly. If the user is not signed in, the app
redirects to `/login` and, after password + MFA, returns to the requested URL (path and query string preserved).

| URL | Page |
|---|---|
| `/` | Dashboard |
| `/accounts` · `/accounts/new` · `/accounts/{accountId}` | Accounts list · open an account · account detail |
| `/documents` · `/documents?highlight={documentId}` | Document vault · with one document highlighted |
| `/goals` | Goals |
| `/market` · `/market/baskets` · `/market/{symbol}` | Market · thematic baskets · security detail (symbol URL-encoded) |
| `/learn` · `/learn/glossary` | Education · glossary |
| `/advisor` · `/advisor/messages` | Advisor booking · secure messages |
| `/notifications` · `/profile` · `/settings` | Notification centre · profile · settings |
| `/login` · `/register` | Sign-in · online enrolment for existing clients (public) |

```html
<a href="https://<electric-wealth-host>/documents?highlight=d1">View your new tax slip</a>
```

**Mobile deep links.** Content produced by the API carries mobile links `electricwealth://<section>/<id>`. The web
app maps `documents`, `accounts`, `advisor`, `market`, `learn`, `notifications`, `profile` and `settings` to the URLs
above; any other section is shown as "Mobile app only". To make a new link work on the web, it must use one of those
sections.

**iframe / Web Component / SDK:** not available.

### 1.4 Security and Sandboxing Defaults

No framing policy, sandbox recommendations or SRI are published, because embedding is not supported. The repo sets no
security headers (no `X-Frame-Options`, `frame-ancestors` or CSP) — these must be added by whoever hosts the
static files.

### 1.5 Lifecycle and Mount/Unmount

Not applicable: there is no mount/unmount API. The app's lifecycle is a browser tab. Session lifecycle (sign-in,
refresh, idle timeout, sign-out) is in [05](05-authentication-integration.md#55-session-lifecycle).

### 1.6 Limitations and Caveats

- Root-of-origin only; no sub-path mount.
- The API must be same-origin at `/api`; there is no build- or run-time setting for another API URL.
- **Rate limiting behind a proxy:** the API limits requests per client IP (300/min). If `/api` is reverse-proxied
  without passing the client IP through, every user shares one budget. Plan the proxy accordingly (see
  [09.5](09-performance-considerations.md#95-network-and-api-cost)).
- Demo behaviour is built in (pre-filled demo email, demo password / MFA code / identities shown as hints); remove
  before any non-demo use.
- Some features exist only in the mobile app; their deep links are not clickable on the web.

