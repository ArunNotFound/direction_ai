# CanonFlow Skills Pack

A curated, blended skills pack for agent-assisted development of CanonFlow,
governed by Hermes Agent. Skills are plain markdown with YAML frontmatter
(agentskills.io-compatible) — drop the `skills/` folders into
`~/.hermes/skills/` (or any compatible host: Claude Code, OpenClaw, Codex).

## Provenance of the blend
- **obra/superpowers** (MIT) — process backbone: TDD, debugging, verification, planning
- **garrytan/gstack** (MIT) — ops discipline: guardrails, retro, session context, spec phases
- **microsoft/SkillOpt** (MIT) — evolution discipline: validation-gated skill edits, protected sections
- **CanonFlow originals** — constitution, F# identity law, agent governance, provenance, banyan bench

## The three-tier structure (Load Line discipline)
- **Tier 0 — PROTECTED:** `canonflow-constitution`, `fsharp-identity-law`, `agent-governance`.
  Durable laws. No agent, optimizer, or learning loop may edit content inside
  `<!-- PROTECTED -->` markers. Ever.
- **Tier 1 — Process:** law-driven-development, the-descent, verification-before-completion,
  spec-then-execute, commit-provenance. Editable only through the skill-evolution-gate.
- **Tier 2 — Ops & meta:** guardrails, session-context, weekly-retro, banyan-bench,
  writing-skills, skill-evolution-gate.

## Install (Hermes)
Copy `SOUL.md` content into `~/.hermes/SOUL.md`, copy `skills/*` into the
Hermes skills directory, restart the gateway. Verify with a message like
"which skills govern F# work?" — the constitution and identity law must both surface.
