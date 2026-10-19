# Cross-cutting: API versioning & deprecation

> **Owning repo:** `api-spring` · **Primary pointer:** [`source-docs/electric-wealth-api-spring/api-consumer/14-versioning.md`](../../source-docs/electric-wealth-api-spring/api-consumer/14-versioning.md)

Unversioned URLs; one live contract (`info.version` 0.1.0). In practice the contract is pinned to the Node API's by `ContractParityTest`, and the web app's types are generated from it — any change to paths, parameters, status codes or schema shapes is breaking for the web app. No endpoints are deprecated.

**Further reading:**

- [`15-changelog-and-deprecation.md`](../../source-docs/electric-wealth-api-spring/api-consumer/15-changelog-and-deprecation.md) (Part B)
- [`11-deprecation-policy.md`](../../source-docs/electric-wealth-web/web-app-consumer/11-deprecation-policy.md) (Part B)

> Pointer page only — the standard itself lives in the linked source docs. Do not copy it here.
