---
name: weekly-retro
description: Run weekly (cron) or on "retro". Reviews the week against the tempo self-checks and feeds validated lessons to the learning loop.
tier: 2-ops
triggers: [retro, retrospective, what did we ship, weekly review]
---

# Weekly Retro

Gather: commits (with trailers), merged PRs, law-suite trend, demo-job status,
WORKLOG entries. Then answer the steward's four standing self-checks,
verbatim and honestly:
1. Was this week's artifact code the material could reject, or another document?
2. Was the harness improved instead of the kernel? (ADR-012 — list any
   harness commits and their blocked-task justifications)
3. Did "one more spec section" delay first contact?
4. Did new adjacent ideas enter scope? (they get `post-v1` labels only)

Output: ≤15 lines to the steward's channel — shipped · learned · blocked ·
next single most valuable task. Candidate lessons for skill updates go to
skill-evolution-gate, never applied directly.
Adapted-from: gstack retro (MIT, Garry Tan), stripped of gbrain dependencies.
