---
component_id: web-i18n-and-formatting
repo: web
type: framework
description: >-
  Use when a change touches translations (en-CA / fr-CA resource files), locale switching, or money, date and percentage formatting helpers.
triggers:
  - "i18n"
  - "translations"
  - "en-CA"
  - "fr-CA"
  - "setAppLocale"
  - "formatMoney"
  - "date formatting"
  - "locale"
paths:
  - "src/i18n/**"
  - "src/lib/format.ts"
depends_on: []
consumed_by:
  - "web-accounts"
  - "web-advisor"
  - "web-app-shell"
  - "web-dashboard"
  - "web-documents"
  - "web-education"
  - "web-goals"
  - "web-market"
  - "web-notifications"
  - "web-profile"
  - "web-registration-wizard"
  - "web-settings"
  - "web-sign-in-and-mfa"
data_entities: []
last_verified_against_commit: 7489670e43b9b3498218e9a1559254ff6a581ea3
last_feature_id: ""
source_doc: docs/web-app-internal/14-internationalization-i18n.md
verification_signal: exported-symbols-diff
---

# Internationalisation and Formatting

> **Repo:** `web` ([repo summary](../../repos/electric-wealth-web/README.md)) · **Type:** `framework` · **Source doc:** [`docs/web-app-internal/14-internationalization-i18n.md`](../../../electric-wealth-web/docs/web-app-internal/14-internationalization-i18n.md)

## 1. Overview

`i18next` with two bundled resource files (`en-CA.json`, `fr-CA.json`) and `setAppLocale`; `format.ts` formats money (minor units / 100), dates and percentages per locale. The active locale is also sent to the API as `Accept-Language` and is part of several query keys.

**Consumed by:** [`web-accounts`](../../components/electric-wealth-web/web-accounts.md), [`web-advisor`](../../components/electric-wealth-web/web-advisor.md), [`web-app-shell`](../../components/electric-wealth-web/web-app-shell.md), [`web-dashboard`](../../components/electric-wealth-web/web-dashboard.md), [`web-documents`](../../components/electric-wealth-web/web-documents.md), [`web-education`](../../components/electric-wealth-web/web-education.md), [`web-goals`](../../components/electric-wealth-web/web-goals.md), [`web-market`](../../components/electric-wealth-web/web-market.md), [`web-notifications`](../../components/electric-wealth-web/web-notifications.md), [`web-profile`](../../components/electric-wealth-web/web-profile.md), [`web-registration-wizard`](../../components/electric-wealth-web/web-registration-wizard.md), [`web-settings`](../../components/electric-wealth-web/web-settings.md), [`web-sign-in-and-mfa`](../../components/electric-wealth-web/web-sign-in-and-mfa.md)

---

## 2. Key Responsibilities

- Provide translations and locale switching.
- Format money, dates and percentages.

**Explicitly NOT in scope:**

- Server-side bilingual content — owned by [`api-education`](../../components/electric-wealth-api-spring/api-education.md)

---

## 3. Change-Impact Summary

| Change Type | Likely Blast Radius | Verification Signal |
|---|---|---|
| Exported symbols / shared behaviour | Every dependant: `web-accounts`, `web-advisor`, `web-app-shell`, `web-dashboard`, `web-documents`, `web-education`, `web-goals`, `web-market`, `web-notifications`, `web-profile`, `web-registration-wizard`, `web-settings`, `web-sign-in-and-mfa` | `exported-symbols-diff`, then the repo's full gate |
| Internal refactor (no interface change) | This component only | Repo gate (`./mvnw test` or `npm run verify`) |
| Data-entity schema change | Indirect — via the features that use this foundation | Repo gate |
| Dependency upgrade | Whole repo | `./mvnw verify` or `npm run build` |

**Upstream dependents to notify:** `web-accounts`, `web-advisor`, `web-app-shell`, `web-dashboard`, `web-documents`, `web-education`, `web-goals`, `web-market`, `web-notifications`, `web-profile`, `web-registration-wizard`, `web-settings`, `web-sign-in-and-mfa`

**Downstream services to monitor post-change:** None.

---

## 4. Recent Activity

**Last touched by feature:** _none — initial KB build (2026-10-19)_

**What that feature changed in this component:** n/a — no feature has touched this component since initial extraction.

**Last verified against commit:** `7489670e43b9b3498218e9a1559254ff6a581ea3` (2026-10-19)

**Open follow-ups from extraction:**

- Locale files still carry mobile-only sections (`tabs`, `more`, `change`) (Part A § 1.1).
