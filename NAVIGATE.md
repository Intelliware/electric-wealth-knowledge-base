# NAVIGATE — Electric Wealth Knowledge Base

This is the knowledge base for **Electric Wealth**. Every framework skill that needs grounding reads this file first.

## Read order

```
1. ARCHITECTURE.md (sections 0–4) for system shape, the solution-level
   pointers (§ 0), and the capability index (§ 4).
2. shared/solution-context/* for project-scoped intent (mission, personas,
   goals, constraints, integrations, ownership). Cheap to grep by trigger
   keyword; never read all six leaves blindly.
3. shared/ai-rules/project-context.md when about to write or modify code in
   any repo. This is the contract for tech stack, language conventions,
   testing discipline, and architectural must-nots.
4. shared/operations/* when the work touches deployment, secrets, SLOs, or
   on-call concerns.
5. graph.json for blast radius walks.
6. components/<repo>/<id>.md for the specific components in scope.
7. shared/entities/<id>.md for cross-component data semantics.
```

In this KB `<repo>` in paths is the folder slug `electric-wealth-<code>` (`electric-wealth-api-spring`,
`electric-wealth-web`, `electric-wealth-db`); the `repo` field inside component frontmatter is the code
(`api-spring`, `web`, `db`). Items 2–4 are empty until `capture-solution-context`, `generate-ai-rules` and
`document-operations` run.

## Selection rule

Never read the whole KB. Use `navigate-knowledge-base` (or its inline equivalent: capability lookup in
ARCHITECTURE.md § 4 + one-hop graph expansion) to choose the minimal component set. Default cap: 7 components.

## Freshness rule

If `last_verified_against_commit` on a component does not match its repo's current HEAD AND the changed files overlap
the component's `paths:`, treat the component body as suggestive and verify against source.

## Update rule

When you finish a feature, do not edit this KB by hand. Invoke `update-knowledge-base` with the implementation
increment, the git diff, and the feature ID.

## Repo cross-link

Each repo has its own NAVIGATE.md at `repos/<slug>/NAVIGATE.md` — read that when narrowing into a single repo:
[`api-spring`](repos/electric-wealth-api-spring/NAVIGATE.md) · [`web`](repos/electric-wealth-web/NAVIGATE.md) ·
[`db`](repos/electric-wealth-db/NAVIGATE.md).

## Schema reference

Component frontmatter schema: `config/SCHEMAS.md` § 3.2 in A.I.D.E. graph.json schema: § 3.3. Edits MUST conform;
`verify-knowledge-base-coverage` will reject non-conformant changes.

## Source docs

`source-docs/<slug>/` holds each repo's Part B (consumer) RE output and is owned by the RE stage — never edit it from
here. Part A (internal) docs stay in each source repo under `docs/`.
