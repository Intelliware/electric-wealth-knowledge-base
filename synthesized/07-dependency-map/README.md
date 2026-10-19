# 07 — Dependency Map

Canonical home: [`graph.json`](../../graph.json) (40 nodes, 110 edges) and the Mermaid diagram in [`ARCHITECTURE.md` § 5](../../ARCHITECTURE.md#5-dependency-graph).

```bash
# What does a component depend on?
jq -r '.edges[] | select(.from=="api-accounts") | "\(.kind)\t\(.to)"' graph.json
# Who depends on it (blast radius, one hop)?
jq -r '.edges[] | select(.to=="api-accounts") | "\(.kind)\t\(.from)"' graph.json
# All cross-repo edges
jq -r '(.nodes | map({(.id): .repo}) | add) as $r | .edges[] | select($r[.from] != $r[.to]) | "\(.from) -\(.kind)-> \(.to)"' graph.json
```
