# Integration pattern: Sign-in, MFA and Session Lifecycle

> **Source:** [`source-docs/electric-wealth-api-spring/api-consumer/02-authentication.md`](../../source-docs/electric-wealth-api-spring/api-consumer/02-authentication.md) (Part B)

**Participating components:** [`web-sign-in-and-mfa`](../../components/electric-wealth-web/web-sign-in-and-mfa.md), [`web-api-client`](../../components/electric-wealth-web/web-api-client.md), [`api-auth-sessions`](../../components/electric-wealth-api-spring/api-auth-sessions.md), [`api-request-pipeline`](../../components/electric-wealth-api-spring/api-request-pipeline.md), [`db-identity-and-auth`](../../components/electric-wealth-db/db-identity-and-auth.md)

```mermaid
sequenceDiagram
    participant UI as LoginPage / MfaPage
    participant Auth as AuthContext (web-sign-in-and-mfa)
    participant API as api-auth-sessions
    participant Pipe as api-request-pipeline
    participant DB as db-identity-and-auth
    UI->>Auth: login(email, password)
    Auth->>API: POST /api/auth/token
    API->>DB: insert session (mfa_verified = 0)
    API-->>Auth: tokens, mfaRequired: true (phase = mfa)
    UI->>Auth: verifyMfa(code)
    Auth->>API: POST /api/auth/mfa/verify (Bearer)
    API->>DB: mfa_verified = 1, extend expiry
    API-->>Auth: 200 (phase = authenticated)
    Note over Auth,Pipe: every protected call: Bearer token checked by BearerTokenFilter
    Auth->>API: POST /api/auth/refresh (every 10 min)
    API->>DB: revoke old pair, insert new pair
    Auth->>API: POST /api/auth/logout (idle 15 min, user, or 401)
    API->>DB: revoked = 1
```

Sessions live in the shared `sessions` table, so tokens are interchangeable with the Node API. Registration completion skips the MFA step by returning an already verified session (see `online-enrolment`).
