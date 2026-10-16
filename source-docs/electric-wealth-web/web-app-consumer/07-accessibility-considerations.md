---
title: "Accessibility Considerations"
block: reverse-eng/web-app/consumer/accessibility-considerations
part: B
source_repo: electric-wealth-web
source_version: 0.1.0
source_commit: bbb4907dd0d866223742c529ea65a75ec8af576f
extracted: 2026-10-16
---

## 7. Accessibility Considerations

### 7.1 Accessibility Contract

No formal commitment: the repo states no WCAG level, has no accessibility tests or audit results, and no
accessibility statement. The behaviour below is what the code implements; it has not been measured against WCAG.

### 7.2 What the Embed Guarantees

As a standalone app, it provides:

- A "Skip to content" link, landmark regions (header, labelled main navigation, main) and one `h1` per page.
- A visible focus outline on keyboard focus.
- A text label for every form field; labels on icon-only controls (notification bell with unread count, user menu,
  remove-from-watchlist).
- Status and error messages announced to screen readers (`role="status"` / `role="alert"`, polite live region for
  loading and for saving settings).
- Change values shown with ▲/▼ glyphs and text, not colour alone; charts have a text label and the asset-allocation
  chart has a text legend.
- `prefers-reduced-motion` respected for the loading spinner; light, dark and system themes.
- The page language (`<html lang>`) kept in sync with the chosen language.

### 7.3 What the Host MUST Provide

Nothing (no host). Operators should not add wrappers that remove the focus outline or override the document
language.

### 7.4 iframe-Specific Patterns

Not applicable.

### 7.5 Keyboard Model

Standard browser behaviour: Tab / Shift+Tab through links, buttons and fields; Enter / Space to activate. Specific
behaviour: the document-sharing dialog is a native modal (focus is contained, Esc closes it); the user menu is a native
disclosure (`<details>`); in the advisor message box Enter sends and Shift+Enter adds a new line. There are no custom
keyboard shortcuts.

### 7.6 Screen Reader Behavior

Toggle buttons expose their state (`aria-pressed`); wizard progress is exposed as a progress bar ("Step n of 7");
unread notifications are announced as "(unread)"; chat messages are prefixed with "You" or the advisor's name.
Known gaps: the cancel-meeting confirmation uses the browser's `confirm()` dialog; on narrow screens tables hide their
column headers; a few labels are English-only (skip link, pagination label).

### 7.7 Localization and Accessibility

All visible labels and ARIA labels come from the translation files, except the English-only items in
[7.6](#76-screen-reader-behavior). The `lang` attribute switches between `en-CA` and `fr-CA`, so screen readers use the
right voice.

### 7.8 Reporting Accessibility Issues

No reporting channel is defined in the source or project configuration. Raise issues with the team that owns
`electric-wealth-web`.

