# Gemini Work Order — GSTFlow: Close-Out Before First User

> Self-contained. GSTFlow is at 20 commits and the engine is DONE. This order
> contains ONLY the closing tasks that make the repo trustworthy to a stranger.
> Do NOT add features. Do NOT start EDIFlow. Do NOT touch CanonFlow.
> Estimated total: one session.

## CONTEXT (5 lines)
- GSTFlow: offline F# GST-invoice validator. Engine complete: GSTIN mod-36,
  place-of-supply, tax-split law, sanity rules, five-level RuleOutcome,
  VerdictEnvelope + canonical JSON, batch CLI with duplicate detection.
- Both runtimes (NativeAOT CLI + Fable/WASM browser) run the same F# core.
- 9 planted specimens + 4 REAL invoice fixtures + mock PDF corpus in repo.
- Standing law: no `|| true` in CI; every test must be able to fail; no claim
  may exceed its mechanism.
- Style: F# with Haskell discipline — DUs, Result, pure functions, no OOP residue.

---

## TASK 1 — Complete the Capability Matrix (HIGHEST PRIORITY)
The README's Capability Matrix currently lists ONLY "Supported" rows. The
missing half is the more important half. A matrix that only lists strengths
reads like vendor marketing; a matrix that names its limits is the single most
persuasive artifact for a Chartered Accountant.

Add these rows (adjust wording to match actual code state — verify each before
writing it):

| Feature / Domain Area | Status | Description |
| :--- | :--- | :--- |
| **Reverse Charge (RCM)** | Detected, Not Adjudicated | Flags possible RCM; returns `Unknown` / review-required. Does not determine RCM liability. |
| **SEZ / Export / Deemed Export** | Not Supported | Returns `NotSupported`. Never silently passes. |
| **Credit / Debit Notes** | Structural only | Reference presence validated; reversal semantics not adjudicated. |
| **IRN / E-Invoice** | Format only | Length/structure checked. **NOT** cryptographic signature verification. |
| **E-Way Bill** | Not Supported | Out of scope. |
| **GSTN Filing / Submission** | Never | Constitutional non-goal. GSTFlow validates; it never files. |
| **HSN rate correctness** | Format only | HSN structure validated; rate-to-HSN applicability NOT verified. |
| **Multi-currency** | Not Supported | INR only. |

RULE: if the code cannot do it, the row must say so. If unsure whether a row is
accurate, READ THE CODE, do not guess. An inaccurate "Supported" is worse than
an honest "Not Supported."

## TASK 2 — Soften unproven guarantee language
README currently says CI "**guarantees** byte-identical validation reports."
- If the agreement test has been demonstrated to FAIL when perturbed (red run),
  keep "guarantees" and link the CI run.
- If it has NOT been demonstrated to fail, change to: "Our CI pipeline verifies
  that both environments yield byte-identical validation reports."
Then DO the demonstration: perturb one serialization, capture the red CI run,
revert, and record the run link in the README. A test never seen to fail is not
evidence.

## TASK 3 — Rule metadata completeness gate
Every RuleId must have: Category, Reference (regulation/section), Confidence,
MessageKey — and a template in EVERY shipped language (currently EN + HI).
Add a CI check that FAILS the build if any RuleId lacks a translation in any
shipped language. No verdict may ever surface to a user as a bare rule code.

## TASK 4 — The falsifier evidence file
Create `docs/FALSIFIER_LOG.md` with this exact skeleton, left blank:

```
# Falsifier Log — Real-World Validation

| Date | Who | Role | Invoices | Result | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| | | | | | |

## Rule review (CA)
- Reviewer:
- Date:
- Rules flagged for false-verdict risk:
- Rules confirmed correct:

## Verdict
- [ ] One real business validated one real invoice correctly
- [ ] One CA reviewed the rule list for false-verdict risk
```

This file is the project's actual scoreboard. Everything else is preparation.

## TASK 5 — Final hygiene sweep
- Confirm zero `|| true` anywhere in CI (should already be zero — verify).
- Confirm no Vite/React default assets remain in the playground.
- Confirm .gitignore covers bin/, obj/, out/, dist/, node_modules/, *.pdf
  (real invoices must never be committed — PII).
- Verify all 9 specimens + 4 real fixtures run in CI and ASSERT expected
  verdicts (not just execute).

---

## DEFINITION OF DONE
- [ ] Capability Matrix has BOTH halves; every row verified against code.
- [ ] Guarantee language matches proven mechanism; agreement test shown to bite.
- [ ] Translation-completeness gate in CI (build fails on missing template).
- [ ] `docs/FALSIFIER_LOG.md` exists, blank, waiting.
- [ ] Hygiene sweep clean.

## DO NOT
- Do not add rules, features, modes, storage, voice, PDF-OCR, or platforms.
- Do not create Canon.Verification or modify CanonFlow.
- Do not begin EDIFlow.
- Do not write any claim the code cannot demonstrate.
