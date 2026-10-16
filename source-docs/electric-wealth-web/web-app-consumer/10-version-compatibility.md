---
title: "Version Compatibility"
block: reverse-eng/web-app/consumer/version-compatibility
part: B
source_repo: electric-wealth-web
source_version: 0.1.0
source_commit: bbb4907dd0d866223742c529ea65a75ec8af576f
extracted: 2026-10-16
---

## 10. Version Compatibility

### 10.1 Versioning Scheme

`package.json` version `0.1.0` (`private: true`, not published). There are no git tags, releases or changelog in the
repo; the version has not changed since the first commit (12 commits, 2026-10-06 to 2026-10-15). Documentation
versions follow `0.1.0-doc.N`.

### 10.2 Support Window

None defined.

### 10.3 Browser Compatibility

No supported-browser list is published or tested. The build targets Vite 8's default modern-browser baseline (no
polyfills), and the app needs: ES2022, `Intl.NumberFormat` / `Intl.DateTimeFormat`, the `<dialog>` element,
`<details>`, CSS `color-mix()` and custom properties, `prefers-color-scheme`, `sessionStorage` / `localStorage`, and
`HTMLFormElement.requestSubmit()`. Current evergreen Chrome, Edge, Firefox and Safari meet these.

### 10.4 Host Framework Compatibility

Not applicable (not embeddable).

### 10.5 Pinning Strategy

Not applicable for integrators. Internally, the web app is pinned to a specific **API contract** at build time: its
API types are generated from `electric-wealth-api-spring/openapi.json` and the build fails type-checking if the
contract changes incompatibly. Deploy the web app and API from compatible commits.

### 10.6 Compatibility Matrix Across Surfaces

| Web app | Electric Wealth API | Database |
|---|---|---|
| 0.1.0 @ `bbb4907` | 0.1.0 @ `8a61cde` (`openapi.json` matches — `npm run check:api` passed 2026-10-16) | `electric-wealth-db` migrations `001`–`007` (via the API) |
| Mobile deep links | Sections `documents`, `accounts`, `advisor`, `market`, `learn`, `notifications`, `profile`, `settings` | — |

Runtime for development and builds: Node.js 24 (`.nvmrc` 24.19.0).

### 10.7 Known Compatibility Quirks

- Deep links for mobile-only sections are shown as "Mobile app only".
- The API offers `GET /api/market/quote`, which the web app does not use.
- The app cannot be served under a sub-path or call an API on another origin.

### 10.8 Upgrading

There is nothing for integrators to upgrade. For maintainers: after an API contract change, run `npm run gen:api`,
fix any type errors, and deploy web and API together.

