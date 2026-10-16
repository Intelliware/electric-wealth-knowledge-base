---
title: "Localization Integration"
block: reverse-eng/web-app/consumer/localization-integration
part: B
source_repo: electric-wealth-web
source_version: 0.1.0
source_commit: bbb4907dd0d866223742c529ea65a75ec8af576f
extracted: 2026-10-16
---

## 8. Localization Integration

### 8.1 Supported Locales

| Locale | Language | Status |
|---|---|---|
| `en-CA` | Canadian English | Default and fallback |
| `fr-CA` | Canadian French | Complete: same 440 translation keys as `en-CA` |

### 8.2 Setting the Locale

Only the end user sets the language, with the EN/FR toggle in the top bar (also on the sign-in and enrolment
screens) or in Settings. On first visit the app uses French if the browser language starts with `fr`, otherwise
English. The choice is remembered in that browser (`localStorage`).

Not supported: a `?lang=` parameter, a locale path segment, or any host-set locale. The user's saved `language`
preference on the API is written when they change language while signed in, but is **not** read back to set the
language on sign-in.

Every API request carries `Accept-Language: en-CA` or `fr-CA`. The API localises the dashboard, education content,
glossary and enrolment security questions; other API text is returned as stored.

### 8.3 Date, Number, Currency Formatting

All formatting uses the browser's `Intl` APIs with the app locale:

| Value | Format |
|---|---|
| Money | API amounts are integer cents; shown as currency with 2 decimals in the account's currency (e.g. `$1,234.56` / `1 234,56 $`) |
| Percent | 2 decimals with sign (`+0.65%` / `+0,65 %`); API value `0.65` means 0.65 % |
| Dates | Medium date style (UTC for calendar dates) |
| Date-times | Medium date + short time, in the browser's time zone |
| Advisor slots | 24-hour API times shown in the locale's style ("2:30 p.m." / "14 h 30") |
| File sizes | B / KB / MB / GB with locale number formatting |

### 8.4 Translation Overrides

Not supported. Strings are bundled at build time; changing them means editing `src/i18n/en-CA.json` and
`src/i18n/fr-CA.json` and rebuilding.

### 8.5 Right-to-Left (RTL) Support

Not supported; no RTL locale or `dir` handling exists.

### 8.6 Adding a New Locale

A code change: add a JSON file with all keys, register it in `src/i18n/index.ts`, extend the `AppLocale` type,
`getAppLocale()` and the language toggles (currently a two-way EN/FR switch), and add the language to the API's
`Accept-Language` handling and preferences enum if the API should localise for it.

### 8.7 Locale-Aware URL Patterns

None. URLs are the same in both languages.

