---
title: "Versioning"
block: reverse-eng/api/consumer/versioning
part: B2
source_repo: electric-wealth-api-spring
source_version: 0.1.0
source_commit: 8a61cdeafb4537b16221d7aa0e9300010abd3387
extracted: 2026-10-15
---

## 14. Versioning

**Versioning model:** Unversioned URLs. The contract version is `info.version` in the spec (`0.1.0`, same as the
Maven artifact version).

**How to specify a version:**

n/a — clients cannot select a version; there is one live contract.

**What counts as a breaking change (major bump only):**

No policy is written down in the repo. In practice the contract is pinned to the Node API's: the API's test suite
fails if any implemented endpoint's paths, methods, status codes, parameters, or request/response shapes
(property names, required fields, types, enum values) differ from the Node API's spec. Treat any change to those
as breaking for the web app, whose TypeScript types are generated from the spec.

**What is additive (minor bump, no major change):**

Descriptions, examples and formats (ignored by the parity check); new endpoints that also exist in the Node API.

**Version lifecycle:**

Single version (0.1.0); no lifecycle defined.

**Sunset signal:**

None — no `Deprecation`/`Sunset` headers are sent.

**Supported versions today:**

| Version | Status |
|---|---|
| 0.1.0 | Current (only) |

**Recommended consumer practice:**

Regenerate client types from [openapi.yaml](../openapi.yaml) (or `GET /openapi.json`) whenever the API changes,
ignore unknown response fields, and branch on error `code`, not `message`.

**Cross-references:**

[15-changelog-and-deprecation.md](15-changelog-and-deprecation.md) · [07-response-format.md](07-response-format.md)
