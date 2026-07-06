---
name: spec-then-execute
description: Use when turning vague intent into work, or when holding a spec for a multi-step task. Architect/executor split with bite-sized tasks.
tier: 1-process
triggers: [spec this, plan this, break down, implement the spec, issue]
---

# Spec, Then Execute

Blend of gstack's five-phase spec skill with superpowers writing-plans /
executing-plans, mapped onto the steward's Opus-architect / Sonnet-executor
pattern.

## Specify (architect model, or steward directly)
Five phases from vague to precise: (1) restate intent in one sentence;
(2) enumerate invariants touched (cite level3-spec sections); (3) edge cases
and FieldClass implications; (4) acceptance = which laws/conformance checks
must pass; (5) out-of-scope list (defend against sprawl — ADR index applies).
Save to `spec/plans/YYYY-MM-DD-<name>.md`.

## Plan for a zero-context executor
Write the plan assuming a skilled developer who knows nothing of this
codebase and has questionable taste: exact files per task, the law to write
first, commands to verify, docs to consult. Bite-sized tasks; each produces
compiling, law-tested code. DRY, YAGNI, frequent commits with provenance
trailers.

## Execute (executor model)
One task at a time, law-driven-development per task,
verification-before-completion per claim, PR at the end. If the spec is
ambiguous mid-task: stop and ask, one precise question — never improvise
architecture.
Adapted-from: gstack spec (MIT, Garry Tan); superpowers writing-plans,
executing-plans (MIT, Jesse Vincent).
