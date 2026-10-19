# Integration pattern: Online Enrolment (Registration)

> **Source:** [`source-docs/electric-wealth-api-spring/api-consumer/05-endpoint-detail-template.md`](../../source-docs/electric-wealth-api-spring/api-consumer/05-endpoint-detail-template.md) (Part B)

**Participating components:** [`web-registration-wizard`](../../components/electric-wealth-web/web-registration-wizard.md), [`web-sign-in-and-mfa`](../../components/electric-wealth-web/web-sign-in-and-mfa.md), [`api-registration`](../../components/electric-wealth-api-spring/api-registration.md), [`api-auth-sessions`](../../components/electric-wealth-api-spring/api-auth-sessions.md), [`db-online-enrolment`](../../components/electric-wealth-db/db-online-enrolment.md), [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md), [`db-goals-and-dashboard`](../../components/electric-wealth-db/db-goals-and-dashboard.md), [`db-notifications`](../../components/electric-wealth-db/db-notifications.md)

```mermaid
sequenceDiagram
    participant UI as web-registration-wizard
    participant API as api-registration
    participant DB as db (enrolment, identity, dashboard, notifications)
    UI->>API: POST /api/registration/start
    API->>DB: insert registrations (30 min TTL)
    UI->>API: POST /api/registration/verify-identity
    API->>DB: match offline_clients (account no., postal code, DOB)
    UI->>API: GET + POST /api/registration/security-questions
    API->>DB: replace registration_security_answers
    UI->>API: POST /api/registration/trusted-contact (optional)
    UI->>API: POST /api/registration/complete
    API->>DB: users, user_profiles, user_preferences, user_security_answers, portfolio_snapshots, notifications, quick_links, offline_clients.enrolled_user_id
    API-->>UI: TokenResponse (mfaRequired: false)
    UI->>UI: startSession(tokens) via web-sign-in-and-mfa, navigate to /
```

Steps must run in order (`STEP_OUT_OF_ORDER`); an expired or finished draft yields `REGISTRATION_NOT_FOUND`, which resets the wizard to step 1.
