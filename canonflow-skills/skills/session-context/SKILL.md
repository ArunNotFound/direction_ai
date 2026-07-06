---
name: session-context
description: Use at session start (hydrate) and before ending or switching tasks (save). Continuity across sessions and platforms.
tier: 2-ops
triggers: [save progress, save state, resume, where were we, context save, hydrate]
---

# Session Context

## Hydrate (session start)
Read, in order: `WORKLOG.md` tail → open PRs and their CI state → `spec/plans/`
entries marked in-progress → last retro's action items. Summarize in ≤5 lines
before taking any action. This is the context-hydrator pattern: the work-item
record, not memory, is the source of truth.

## Save (before ending / switching)
Append to `WORKLOG.md`: date · task · state (exact next command to run) ·
open questions for the steward · files touched. Push the branch (PR-only rule
applies). A saved context must let a different model resume with zero
re-explanation — write for the zero-context executor.

Never rely on conversational memory for state that belongs in the repo.
Adapted-from: gstack context-save / context-restore (MIT, Garry Tan).
