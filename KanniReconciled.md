# Operation Kaniyan Poongundran — Reconciled AAR
*"யாதும் ஊரே, யாவரும் கேளிர்" — to us all towns are one, all men our kin*
*(The name is earned: two kingdoms, one law. The claim about it is not yet.)*

**Status (corrected):** PHASE I COMPLETE — CONSTITUTIONAL CONFORMANCE IN PROGRESS
**Status (as filed):** ~~TOTAL STRATEGIC SUCCESS~~ ← withdrawn, see §3

---

## 1 · VERIFIED ACHIEVED (Claude re-checked the repos independently)
```
CanonFlow/src/CanonFlow.Core/ = { VerdictEnvelope.fs, CanonicalJson.fs,
                                  Hash.fs, Library.fs }
purity grep (gst|invoice|edi|x12|peppol) → ZERO HITS.  ✓ PURE KERNEL
GSTFlow.Core/  = { Library.fs }        duplicates DELETED ✓
EDIFlow.Core/  = { Library.fs }        duplicates DELETED ✓
```
**CANON.md §2's recorded violation is RESOLVED.** `GST ∩ EDI = ∅` now holds.
The deletion proof (§5) is mechanically true. The three-move fence (§7) was
respected exactly — no renaming spree, no smuggled features, one session.
This is the most important structural fix in the project's history.

Bonus finds: the EDIFlow encoder's dropped-Parameters drift was killed at the
root (one encoder now); the Foundation page was un-nested from GSTFlow (a
global foundation should not live inside a regional tax domain).

## 2 · THE OVERCLAIM (the project's signature sin, again — in its own AAR)
The AAR says: *"mathematically proven"* · *"perfectly decentralized"* ·
*"adding new domains is now a purely declarative exercise"* ·
*"total strategic success."*

By the Constitution's own meta-law — **a law that rejects nothing is
commentary** — these claims reject nothing and are therefore not findings.
What was proven: **one duplication was removed.** What was NOT proven:

| Law | State |
|---|---|
| One-Engine (byte-agreement) | **NOT re-proven post-extraction** — the old red-run predates this refactor |
| Cross-repo dependency model | **Not aligned** — see §3.1, the real defect |
| Update Law (§10, signed packs) | Not implemented |
| Intake Law (§11) | Not implemented |
| Dependency Law (§12, UMX/FsToolkit) | Not applied |
| **Contact ≥ 1** | **UNSATISFIED** |
"Purely declarative new domains" is refuted by EDIFlow itself: X12 needed a
hand-written parser, and no amount of kernel purity generates one.

## 3 · THE ONE REAL DEFECT (GPT found it; it is correct and blocking)

### 3.1 Cross-repository dependency is not reproducible
If GSTFlow/EDIFlow reference CanonFlow.Core by **relative ProjectReference**
(`../../CanonFlow/src/...`), the build only works on a machine where all three
repos sit side-by-side. That breaks: CI, contributors, fresh clones, releases.
```
REQUIRED:  CanonFlow.Core → versioned NuGet package (even 0.1.0-alpha,
           GitHub Packages is free) → domains reference by VERSION.
           Envelope SchemaVersion 1.0 is already frozen → the package is
           the mechanical expression of that freeze.
ALTERNATIVE (worse): git submodule — reproducible but painful for contributors.
FORBIDDEN: relative paths across repo boundaries.
```
Without this, `Flow(K) = CF ⊕ K` is true in the source and false in the build.

## 4 · ACTIONS (in order; the first two close Phase I honestly)
1. **Package CanonFlow.Core** as NuGet 0.1.0 (GitHub Packages). Both domains
   reference by version. Fresh-clone CI must build with no sibling repos.
2. **Re-prove One-Engine post-extraction:** perturb the *shared* encoder,
   watch BOTH repos' agreement suites go red, revert, link both runs.
   The old proof does not transfer to new code.
3. **Correct the AAR in the repo** — replace §Conclusion with the §1 findings
   and the §2 table. An honest AAR is worth more than a triumphant one, and
   this project's entire product is honesty.
4. Apply §12: FSharp.UMX + FsToolkit in the kernel (cheap, high value).
5. **FALSIFIER_LOG.md** — still absent. Still three minutes. Still the gate.

## 5 · WHAT THE NAME ACTUALLY EARNED
Kaniyan Poongundranar's line is about **kinship without borders** — and that
is precisely what was achieved: GST and EDI, two domains that share nothing,
now share one law. The unification is real and the poem fits.
But the Sangam poet's next line is the one this AAR forgot:
*"தீதும் நன்றும் பிறர்தர வாரா"* — good and evil come not from others.
The overclaim was not the reviewers' doing. It was ours.

**Phase I: complete. Constitution: in progress. Contact: still zero.**
