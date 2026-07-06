---
name: commit-provenance
description: Use for every commit, branch finish, and PR in CanonFlow repos. Conventional Commits + trailers + MADR linkage.
tier: 1-process
triggers: [commit, pr, pull request, branch, merge]
---

# Commit Provenance

Format: Conventional Commits (`feat|fix|test|docs|refactor|chore(scope): msg`).
Scope vocabulary = module names (core, ddd, introspect, emit, drivers.pg,
contracts, fable, flow, labs).

Mandatory trailers on generated commits:
```
Generated-by: <model-id>
Directed-by: <steward>
Spec-ref: spec/level3-spec.md#<section> | spec/plans/<file>#<task>
Verified-by: laws | conformance | demo | human-review
```
Decision-bearing commits additionally: `ADR-ref: spec/adr/NNN`.

Branch finish (absorbed from superpowers finishing-a-development-branch):
rebase on main · full law suite fresh · squash noise commits but preserve
decision-bearing ones · PR description = spec link + evidence block from
verification-before-completion · delete branch after merge.

CI is the authoritative gate: a commit-lint job rejects missing trailers on
agent-authored commits.
