---
name: verification-before-completion
description: Use when about to claim anything is done, fixed, green, or passing — before commits, PRs, or status reports.
tier: 1-process
triggers: [done, complete, fixed, passing, ready, ship]
---

# Verification Before Completion

Iron law: **no completion claims without fresh verification evidence.**
Claiming without verifying is dishonesty, not efficiency.

Gate, before any status claim or satisfaction:
1. IDENTIFY the command that proves the claim (`dotnet test` for laws,
   conformance suite for drivers, the demo job for stranger-readiness,
   `npm test` for TS agreement).
2. RUN it fresh and complete — in this session, not from memory.
3. READ the full output. Partial green + "probably fine" = red.
4. REPORT with the evidence attached (exit code + relevant lines) and the
   Verified-by trailer set accordingly.

Special CanonFlow rule: "the law passes" may only be claimed from an FsCheck
run in the current session with the seed recorded, so failures are
replayable.
Adapted-from: superpowers verification-before-completion (MIT, Jesse Vincent).
