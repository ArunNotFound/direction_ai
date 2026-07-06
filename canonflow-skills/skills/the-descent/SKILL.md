---
name: the-descent
description: Use for ANY bug, failing law, unexpected behavior, or zero-hit result — before proposing fixes. Root cause via six gated rungs.
tier: 1-process
triggers: [bug, failing test, failing law, unexpected, zero hits, debug]
---

# The Descent

Blend of the steward's six-rung triage protocol with superpowers
systematic-debugging. Iron law: **no fixes without root cause. Symptom
patches are failure**, especially under time pressure — rushing guarantees
rework.

Rungs (each is a gate; do not skip down):
1. **Reproduce** — deterministic minimal repro, or stop and instrument.
2. **Read the actual error** — full text, full stack, no paraphrase.
3. **Localize** — which layer: algebra / planner / driver / transport /
   store? Use the type system: what does the compiler already prove?
4. **Shrink** — ddmin-style predicate narrowing ("The Shrink"): bisect the
   input/query until the smallest failing case remains. For zero-hit
   diagnosis, widen the predicate stepwise until hits appear; the boundary
   is the bug's address.
5. **Hypothesize once** — a single falsifiable cause, with the experiment
   that would disprove it. Run the experiment before touching the fix.
6. **Fix at cause + law** — the fix ships with a new named property that
   would have caught it (law-driven-development rung 4).

If two hypothesis cycles fail, stop and escalate to the steward with the
shrunk case — do not thrash.
Adapted-from: superpowers systematic-debugging (MIT, Jesse Vincent).
