---
title: "Event and Callback API"
block: reverse-eng/web-app/consumer/event-and-callback-api
part: B
source_repo: electric-wealth-web
source_version: 0.1.0
source_commit: bbb4907dd0d866223742c529ea65a75ec8af576f
extracted: 2026-10-16
---

## 6. Event and Callback API

The web app publishes **no events or callbacks** to other products. The subsections below are kept so readers can see
each surface was checked.

### 6.1 Event Surfaces

| Surface | Supported |
|---|---|
| DOM `CustomEvent`s for a host | ❌ |
| `postMessage` to a parent frame | ❌ |
| SDK event emitter | ❌ |
| Server-side webhooks | ❌ |

The app uses one browser event internally (`ew:unauthorized` on `window`, to trigger sign-out). It is an
implementation detail, not a contract.

### 6.2 Lifecycle Events

None (no ready / mounted / resized / signed-in / signed-out events).

### 6.3 Domain Events

None. Business events (goal created, appointment booked, document shared, message sent) are visible only through the
Electric Wealth API's data.

### 6.4 Subscribing — SDK / Web Component

Not applicable.

### 6.5 Subscribing — postMessage (iframe)

Not applicable.

### 6.6 Callbacks (Synchronous Hooks)

None.

### 6.7 Event Versioning

Not applicable.

### 6.8 Ordering and Delivery Guarantees

Not applicable. Within the app, notifications are refreshed every 60 seconds while signed in; there is no push channel.

