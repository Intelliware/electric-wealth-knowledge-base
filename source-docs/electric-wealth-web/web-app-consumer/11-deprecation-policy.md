---
title: "Deprecation Policy"
block: reverse-eng/web-app/consumer/deprecation-policy
part: B
source_repo: electric-wealth-web
source_version: 0.1.0
source_commit: bbb4907dd0d866223742c529ea65a75ec8af576f
extracted: 2026-10-16
---

## 11. Deprecation Policy

No deprecation policy is defined in the repo or project configuration. This section records what would count as a
breaking change for the integration surface that exists (links into the app) so a policy can be set later.

### 11.1 What Counts as a Breaking Change

- Removing or renaming a page URL listed in [01.3](01-embedding-options.md#13-quick-start-per-mode), or changing the
  meaning of its parameters (`{accountId}`, `{symbol}`, `?highlight=`).
- Removing a deep-link section mapping (`electricwealth://<section>/…`).
- Moving the app off the root of its origin, or the API off same-origin `/api`.
- Changing sign-in so that existing users cannot sign in (e.g. new mandatory step) without notice.

### 11.2 What Is NOT a Breaking Change

- Visual design, layout, copy and translations.
- HTML structure and CSS class names (never a contract).
- Adding pages, URLs or deep-link sections.
- Internal code structure, libraries and bundle file names (hashed).

### 11.3 Deprecation Lifecycle

None defined.

### 11.4 Communication Channels

None defined. The repo has no changelog or release notes.

### 11.5 Deprecation Markers

None in use; no URL or feature is marked deprecated.

### 11.6 Emergency Breaking Changes

No process defined.

### 11.7 Embedder Recourse

Not applicable (no embedders). Teams linking to the app should link to top-level pages where possible.

### 11.8 Currently Deprecated Surface

Nothing is deprecated. Unused strings ported from the mobile app (`tabs`, `more`, `change` translation sections) remain
in the locale files but are not an external surface.

