---
name: canonflow-constitution
description: Load for ANY CanonFlow work — coding, planning, review, docs. The constitutional invariant, phase gates, and ADR index that all work must serve.
tier: 0-protected
triggers: [canonflow, canon.core, refined, lattice, introspect, emit, fieldclass]
---

# CanonFlow Constitution

<!-- PROTECTED -->
## The Law
`introspect (emit domain) ≅ domain` — a retraction pair with classified loss.
Every field maps to `Lossless | Widened | Narrowed | Unrepresentable`; loss is
reported as data, never silently absorbed. Everything in the repo serves this
law or is deleted. The law is the spec; FsCheck properties are its court.

## Phase gates (falsifiable; no gate, no next phase)
0 Foundation → IP clearance GREEN (blocking) · 1 Kernel → all algebraic laws
pass `dotnet test` · 2 Brownfield → 30-min stranger demo green vs live Postgres
in CI · 3 Contracts → TS client rejects exactly what F# rejects · 4 Greenfield
→ round-trip law passes on all four drivers (= v1.0) · 5 Flow → kill-and-resume
chaos test: no dupes, no gaps, per-key order.

## ADR index (never re-argue; cite by number)
001 state-based DDD, not event sourcing · 002 no migration engine ·
003 brownfield first · 004 one DSL · 005 contracts via JSON Schema/OpenAPI ·
006 Propulsion patterns not runtime · 007 no Oracle in v1 ·
008 Apache-2-preferred deps, CI-enforced · 009 codegen not type providers ·
010 route models by capability · 011 agents PR-only ·
012 harness timeboxed.

## Scope defense
New adjacent ideas get an issue labeled `post-v1` and nothing else.
Migration diffing, ORM features, event stores, Oracle drivers: out of scope,
close with a pointer to this skill.
<!-- /PROTECTED -->

When work conflicts with the constitution, stop and surface the conflict to
the steward — do not creatively reinterpret.
