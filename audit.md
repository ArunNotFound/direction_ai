# CanonFlow — Master Plan v4: The Elevation

> Supersedes v3 (phases, ADRs 001–013, lineage, epistemic map — all still binding,
> referenced here, not repeated). v4 reorganizes execution around ten areas,
> each with an honest current score, a **falsifiable definition of 10/10**,
> and the moves that close the gap.
>
> **Standing rule:** a 10 that cannot be tested is vanity. A 10 pursued out of
> sequence is the graveyard pattern. Scores move only on evidence.

**Constitution (unchanged):** `introspect (emit domain) ≅ domain` with classified loss.
**Elevation order:** 2 → 9 → 3 → 8 → 5 → 7 → 6 → 1 → 4 → 10
(correctness and testing first — they make every other 10 cheaper).

---

## Area 1 · Core Idea — now 9/10
The composition is validated: prior-art audited (MSR 2011 as founding citation),
gap confirmed across six ecosystems, novelty claim revised to composition +
productization. The lattice, FieldClass, Refined, and Fable transpiler all exist.

**10/10 means:** the claim is *demonstrated*, not argued — a real compound CHECK
constraint flows Postgres → FParsec → lattice → Refined proof → Fantomas-clean
Domain.fs → Fable → TypeScript that rejects exactly what F# rejects, on video,
in under a minute.
**Moves:** ship the vertical slice (Areas 2+3 do the work); record the 60-second
demo; put it at the top of README. The idea's last point is earned by the demo,
by definition.

## Area 2 · Mathematical Correctness — now 6/10
De Morgan (both directions) + semantic double negation exist over a real sized
generator; ADR-013 (semantic equality via eval) taken. But: CI has never run,
equivalence is single-assignment sampling, no shrinker, and the law set is
incomplete.

**10/10 means:** full law suite green in CI with history — absorption,
distributivity, complement (a ∧ ¬a ≡ ⊥, a ∨ ¬a ≡ ⊤), identity, annihilation,
De Morgan, involution — over arbitrary lattices with a structural shrinker;
**mutation testing proves the laws are not vacuous** (mutate Lattice.fs, laws
must go red); ADR-013 stage 2 delivered: NNF canonical form so structural
equality works and queries are cache-comparable; Refined's phantom-vs-DU
tension resolved as **ADR-014** (recommended: `Refined<'T,'Tag>` phantom tag
over the DU predicate — proofs distinguishable at the type level again).
**Moves (in order):** one green CI run · `Arb.fromGenShrink` + shrinker ·
complete the law set · mutation audit (epistemic U7) · NNF · ADR-014.

## Area 3 · Developer Experience — now 3/10
CLI exists; README promises outputs the pipeline doesn't yet produce end-to-end;
SqlParser drops identifiers, can't parse AND/OR, and loses precision via pfloat.

**10/10 means:** the 30-minute stranger demo is a green CI job; install is one
line (`dotnet tool install -g canonflow`); generated Domain.fs is
Fantomas-clean and reads like a human wrote it; every error message names the
constraint, the table, and the fix; unparseable CHECKs surface as honest
`Opaque of rawSql`, never silently vanish; two real strangers complete the
demo unassisted and their confusion log is empty of blockers.
**Moves:** fix the three parser bugs (field binding on Constraint leaves,
OperatorPrecedenceParser for AND/OR/NOT, direct decimal parsing) · Opaque
fallback · wire demo end-to-end · package as dotnet tool · stranger test ×2.

## Area 4 · Enterprise Adoption — now 2/10
Nothing is adoptable yet, and that is correct for this phase.

**10/10 means (right-sized for a steward project, not a vendor):** three
organizations run CanonFlow-generated code in production; SemVer honored with
a written compatibility promise; SECURITY.md with a disclosure channel; signed
releases on NuGet with the `CanonFlow.*` reserved prefix; an upgrade path that
has survived one real breaking change gracefully; the Wlaschin test passes —
a rotating average team maintains the *emitted* code without ever reading
Canon.Core.
**Moves:** all post-v1.0 (Phase 4 gate) · reserve NuGet prefix now ·
SECURITY.md + SemVer promise at v1.0 · first production user is likely
yourself at arm's length (a personal project, not the employer — ADR-007
daylight preserved).

## Area 5 · AI Readiness — now 7/10
Skills pack shipped (13 skills, 3 tiers, PROTECTED sections), fleet blueprint
with capability routing, provenance trailer spec, Hermes SOUL. Not yet proven
in motion: no agent-authored PR has been merged through the full chain.

**10/10 means:** the pipeline is routine — agent PRs with complete provenance
trailers merge weekly through spec → implement → adversarial review → laws →
steward; escalation rate from Ornith-A1 is measured and trending down;
commit-lint rejects trailer-less agent commits in CI; the **Banyan Bench has
published data** (N ≥ 20 per arm) settling the verification thesis either way;
SURPRISES.md is alive.
**Moves:** wire commit-lint · run the first full-chain agent PR on a small
task (a driver conformance case is ideal) · stand up the bench in labs/ ·
retro cron live.

## Area 6 · Performance — now 4/10 (deliberately right-sized)
Unmeasured, and mostly irrelevant at this stage: CanonFlow is build-time
tooling — codegen speed matters at developer patience scale, not request scale.
The one hot path is generated query planning.

**10/10 means:** benchmarks exist and run in CI with regression alarms —
introspect a 500-table schema < 30 s; emit + Fantomas format 100 types < 10 s;
lattice normalization O(n) verified on 10⁴-node trees; generated TS validators
within 2× of hand-written Zod on the agreement corpus. **No optimization work
before a benchmark exists that demands it** — performance theater is sprawl.
**Moves:** BenchmarkDotNet project in labs/ at Phase 2 exit · CI perf job at
Phase 4 · nothing else.

## Area 7 · Extensibility — now 5/10
The seams exist (ISchemaProvider, IEmitter — thin ports at the shell, correctly
placed). The thing that makes seams safe — the driver conformance suite — does not.

**10/10 means:** the conformance suite is the contract: any driver passing it
merges without the steward reading its internals; a third-party contributor
has shipped a driver (MySQL or MSSQL are the likely volunteers) using only the
suite + docs; labs/ has demonstrated one experiment dying gracefully without
staining the core; adding a lattice constructor is impossible silently —
exhaustiveness breaks every planner's build (already true; keep it true).
**Moves:** extract the conformance suite from the Postgres driver's tests
(Phase 2) · document "write a driver in a weekend" · DuckDB + SQLite become
the suite's first internal customers (Phase 4).

## Area 8 · Documentation — now 3/10
MANIFESTO and GOVERNANCE are real and good. User-facing docs are absent, and
for a small-language project the docs *are* the community (masterplan §9).

**10/10 means:** Diátaxis-shaped — a tutorial (the 30-minute demo, written),
how-to guides (write a driver, add a constraint kind, consume from Kotlin),
reference (every public symbol XML-doc'd, docs generated in CI so drift fails
the build), and explanation (LINEAGE.md, the ADRs, a plain-language page on
classified loss); README's first line preempts the "is Flow an orchestrator?"
confusion; a stranger's first five minutes never require reading F#.
**Moves:** LINEAGE.md from §14 (Phase 0 debt — ship it) · tutorial written
against the working demo (Phase 2 exit) · doc-gen in CI (Phase 3) · Ornith-A1
drafts, steward curates voice, Gemini audits docs-vs-code drift monthly.

## Area 9 · Testing — now 5/10
Law suite exists (partial), Testcontainers integration test is genuinely
professional-grade. But CI has zero runs, 215 obj/ files are tracked, and
three whole test categories from the blueprint don't exist yet.

**10/10 means:** five suites, all green in CI with visible history — **laws**
(complete + mutation-audited), **conformance** (per driver), **agreement**
(random lattices evaluated in F# and executed in Node, byte-identical
verdicts), **integration** (Testcontainers, as now), **chaos** (Phase 5
kill-and-resume); every FsCheck failure records its seed; every shrunk
counterexample becomes a named regression law and a SURPRISES.md entry.
**Moves:** enable Actions, one green run (today) · `git rm -r --cached '**/obj'`
(today) · agreement harness (the transpiler exists — cash it in early) ·
conformance extraction (Phase 2) · chaos (Phase 5).

## Area 10 · Community — now 1/10
Zero external contact. Correct for now; fatal if still true at Phase 3.

**10/10 means (steward-scale, honestly):** the F# Advent post published and
the demo survived the traffic; two stranger testers *before* the post; five
external contributors merged, at least one via the conformance onramp without
steward hand-holding; issues triaged inside a week (Ornith-A1 drafts, steward
decides); a public roadmap that has visibly said "no" (post-v1 labels) more
often than "yes"; zero rugpulls — Apache 2.0 posture unbroken.
**Moves:** upstream PRs to SqlHydra/Fable as first contact (maintainers are
the channel) · stranger tests at Phase 2 exit · Advent post after the demo is
a CI job, never before · contribution ladder in GOVERNANCE.md.

---

## The Scoreboard (updated only on evidence, reviewed at phase gates)

| # | Area | Now | 10/10 gate evidence |
|---|---|---|---|
| 1 | Core idea | 9 | 60-second end-to-end demo video, real compound CHECK |
| 2 | Mathematical correctness | 6 | Full law set + mutation audit + NNF, green in CI |
| 3 | Developer experience | 3 | Stranger ×2 complete demo unassisted; parser bugs dead |
| 4 | Enterprise adoption | 2 | 3 production orgs; SemVer promise survived a breaking change |
| 5 | AI readiness | 7 | Weekly agent PRs merged; Banyan data published |
| 6 | Performance | 4 | Benchmarks in CI with alarms; targets met, nothing more |
| 7 | Extensibility | 5 | Third-party driver merged via conformance suite alone |
| 8 | Documentation | 3 | Diátaxis complete; doc-drift fails the build |
| 9 | Testing | 5 | Five suites green with history; seeds + regression laws |
| 10 | Community | 1 | Advent post survived; 5 external contributors merged |

**This week, in order:** enable Actions → one green run · untrack obj/ ·
parser: field binding + AND/OR + decimal · Opaque fallback · shrinker.
Five evenings. Areas 2, 3, 9 all move at once — that's what elevation order buys.

*Scores are claims. Claims need instruments. அரண் is measured wall by wall.*
