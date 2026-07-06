---
name: skill-evolution-gate
description: Use whenever any skill in this pack would be created, edited, or consolidated — including by Hermes's own learning loop. SkillOpt discipline without the runtime.
tier: 2-meta
triggers: [update skill, new skill, improve skill, learned, consolidate]
---

# Skill Evolution Gate

Skills are trainable state; treat edits with optimizer discipline
(SkillOpt's contract, adopted without its machinery):

1. **Bounded edits only** — add / delete / replace of specific sections;
   never a wholesale rewrite. State the edit as a diff.
2. **Held-out validation** — an edit is accepted only if it demonstrably
   improves outcomes on tasks *not* used to motivate it. Minimum: replay two
   past WORKLOG tasks the edit claims to help; the edit must not degrade any
   other skill's trigger accuracy.
3. **PROTECTED sections are immutable.** Content inside
   `<!-- PROTECTED -->` markers may not be edited by any agent or loop —
   only the steward, by hand, with an ADR if the change is constitutional.
4. **Known failure modes carried from SkillOpt's own evaluation** — guard
   explicitly against: *validation overfitting* (edits tuned to the replay
   set — rotate replay tasks) and *optimizer-target leakage* (edits that
   encode answers to specific tasks rather than transferable procedure —
   reject any edit naming a specific bug, file, or dataset unless it is a
   named regression law).
5. Every accepted edit: version bump in frontmatter, one-line changelog at
   file bottom, steward notified in the weekly retro.

Adapted-from: microsoft/SkillOpt (MIT) — the validation-gate contract and
protected-section concept; zero runtime code inherited.
