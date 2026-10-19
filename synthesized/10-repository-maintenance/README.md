# 10 — Repository Maintenance

- `update-knowledge-base` — after each shipped feature (appends to [`change-tracker.md`](../../change-tracker.md)).
- `detect-knowledge-base-drift` — PR gate / scheduled sweep for source changes not reflected here.
- `verify-knowledge-base-coverage` — coverage and `verification_signal` checks.
- `source-docs/` is owned by the reverse-engineering stage; re-run RE (not this KB) to refresh it.
