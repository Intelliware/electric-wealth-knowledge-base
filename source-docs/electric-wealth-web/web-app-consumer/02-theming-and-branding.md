---
title: "Theming and Branding"
block: reverse-eng/web-app/consumer/theming-and-branding
part: B
source_repo: electric-wealth-web
source_version: 0.1.0
source_commit: bbb4907dd0d866223742c529ea65a75ec8af576f
extracted: 2026-10-16
---

## 2. Theming and Branding

### 2.1 Theming Model

Theming is **end-user only**. Each signed-in user picks a theme in Settings (`light`, `dark` or `system`), stored in
their API preferences (`PUT /api/preferences`, field `theme`). There is no host- or tenant-level theming, no URL
parameter and no runtime theming API. Before sign-in the app follows the operating system's colour scheme.

### 2.2 Public Theme Tokens

None. The app is styled with CSS custom properties (`--accent`, `--bg`, `--text`, …) in a single stylesheet, but they
are internal implementation details with no stability promise and no way for an external party to set them.

### 2.3 Brand Override Recipe

Not supported. Changing colours, fonts or the product name requires editing the source (`src/styles.css` tokens and
the `common.app_name` string in both locale files) and rebuilding.

### 2.4 Logo and Asset Slots

None. The brand is a ⚡ glyph plus the translated product name ("Electric Wealth"), and `public/favicon.svg`. Both are
fixed in source.

### 2.5 Dark Mode and System Preferences

| User setting | Behaviour |
|---|---|
| `light` | Light palette |
| `dark` | Dark palette |
| `system` | Follows `prefers-color-scheme` |

`index.html` declares `<meta name="color-scheme" content="light dark">`. Reduced-motion users get no spinner animation.

### 2.6 What Is NOT Themable

Everything, from an integrator's point of view: colours, typography, spacing, layout, logo, product name, navigation
and copy. Only the end user's light/dark/system choice is configurable.

