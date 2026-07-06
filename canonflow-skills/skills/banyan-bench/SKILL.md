---
name: banyan-bench
description: Use when measuring the verification thesis or designing any eval. Iterations-to-green methodology plus measurement-failure vigilance.
tier: 2-meta
triggers: [banyan, bench, eval, measure, iterations-to-green, thesis]
---

# Banyan Bench

Hypothesis under test: F# is worse for AI generation but better for AI
verification, and loop quality depends on feedback signal, not first-draft
fluency. Status: **hypothesis until measured** — the manifesto may claim only
what this bench supports.

Protocol:
- Same Level-3-specified task in F#-with-laws and C#-with-unit-tests;
  same model, temperature, and harness; N ≥ 20 attempts per arm.
- Primary metric: iterations-to-green. Secondary: defect-escape rate
  (bugs surviving green), tokens-to-green, identity-law violations per attempt.
- Record seeds and full transcripts in labs/banyan-bench/runs/.

Measurement-failure vigilance (the surprising-result rule): before believing
any surprising outcome, in either direction, audit the instrument first —
task-spec asymmetry between arms, law suites of unequal strength, harness
differences, contamination (the model having seen the task). A result that
flatters the steward's F# identity gets *extra* scrutiny, not less: the bench
exists to catch identity-protective cognition, and catching it is a success.

Publish either way. எண்ணித் துணிக — weighed in runs, not words.
