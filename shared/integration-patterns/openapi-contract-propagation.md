# Integration pattern: OpenAPI Contract Propagation (API → Web types)

> **Source:** [`source-docs/electric-wealth-api-spring/api-consumer/14-versioning.md`](../../source-docs/electric-wealth-api-spring/api-consumer/14-versioning.md) (Part B)

**Participating components:** [`api-contract-and-data-access`](../../components/electric-wealth-api-spring/api-contract-and-data-access.md), [`web-api-client`](../../components/electric-wealth-web/web-api-client.md)

```mermaid
sequenceDiagram
    participant Code as api-spring controllers + DTOs
    participant Spec as openapi.json (api-contract-and-data-access)
    participant Node as Node API spec (electric-wealth-api)
    participant Web as web-api-client (schema.d.ts)
    Code->>Spec: springdoc generates, OpenApiSpecTest fails if committed copy differs
    Spec->>Node: ContractParityTest compares shapes, params, statuses
    Spec->>Web: npm run gen:api (openapi-typescript)
    Web->>Web: npm run check:api + typecheck fail on drift
```

This is the main cross-repo coupling: an API contract change is not done until `schema.d.ts` is regenerated and `npm run verify` passes in the web repo. The KB copy of the contract is `source-docs/electric-wealth-api-spring/openapi.yaml`.
