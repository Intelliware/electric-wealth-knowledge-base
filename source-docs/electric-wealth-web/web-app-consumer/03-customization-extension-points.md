---
title: "Customization and Extension Points"
block: reverse-eng/web-app/consumer/customization-extension-points
part: B
source_repo: electric-wealth-web
source_version: 0.1.0
source_commit: bbb4907dd0d866223742c529ea65a75ec8af576f
extracted: 2026-10-16
---

## 3. Customization and Extension Points

### 3.1 Available Extension Surfaces

| Surface | Available |
|---|---|
| Named slots | ❌ |
| Plugin hooks | ❌ |
| Custom panels | ❌ |
| Action interceptors | ❌ |
| Configuration flags | ⚠️ One operator setting (below); no feature flags |
| Linkable URLs | ✅ The only stable integration surface — see [01.3](01-embedding-options.md#13-quick-start-per-mode) |

The app is a closed product: new behaviour is added by changing its source. Teams that need Electric Wealth data in
their own experience should build on the Electric Wealth API.

### 3.2 Named Slots

Not supported. The layout (top bar, navigation, page area) has no injection points.

### 3.3 Plugin Hooks

Not supported. There is no plugin registry or lifecycle hook.

### 3.4 Custom Panels

Not supported. Pages and navigation entries are fixed in the route table.

### 3.5 Action Interceptors

Not supported. User actions call the API directly; there is no way to intercept or veto them from outside.

### 3.6 Configuration Flags

| Setting | Where | Default | Effect |
|---|---|---|---|
| `API_URL` | `.env` (dev server / `vite preview` only) | `http://localhost:4200` | Where the development proxy forwards `/api/*` |

Nothing is configurable at run time in a built bundle. There are no feature flags (`project.toml`:
`has_feature_flags = false`), no tenant configuration and no remote config.

End users control their own preferences in Settings: theme, language, notification channels (email / SMS / push),
document delivery (digital / paper and digital) and default advisor meeting type. These are stored by the API
(`GET`/`PUT /api/preferences`).

### 3.7 Stability of Extension Points

No extension points exist, so none carry a stability promise. The page URLs in
[01.3](01-embedding-options.md#13-quick-start-per-mode) are the de facto contract; there is no written commitment to
keep them stable (see [11](11-deprecation-policy.md)).

