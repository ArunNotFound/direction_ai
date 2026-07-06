# NEXTSTEPS.md — Consolidated Work Order (Executor: Gemini)

> Supersedes all prior NEXTSTEPS. Cherry-picked from the GPT architecture
> review (validated items only) plus standing constitutional debt.
> Written for a zero-context executor per spec-then-execute.
>
> **Rules of engagement (agent-governance skill applies):**
> PR-only — never push to main. One PR per task below. Every commit carries
> trailers: `Generated-by: gemini-<version>` · `Directed-by: Arun` ·
> `Spec-ref:` (given per task) · `Verified-by: laws`.
> Law first, watch it fail, then implement. No completion claims without a
> fresh `dotnet test` in the PR description with seed recorded.
> **Do not add:** Oracle providers (ADR-007), incremental compilation
> (post-v1), semantic catalog / GraphQL / C# emitters (post-v1), any module
> not named below. If a task seems to need one, stop and ask the steward.

---

## T0 · Witness the Constitution (STEWARD, not Gemini — blocks everything)
Open GitHub → Actions. If no runs exist, enable Actions; push an empty
commit; watch `Laws` go green once. Until this happens every claim below is
unverified by definition. While there, extend `laws.yml`:
`actions/setup-node` step (agreement tests shell out to `node`), then
`bash tests/laws/mutation_audit.sh` as a third step.
**Done when:** one green run visible in Actions history with all three steps.

## T1 · NNF Law Triple  — Spec-ref: ADR-013 stage 2
New kernel math (`Lattice.toNNF`) has zero property coverage. Add to
`tests/laws/LatticeLaws.fs`:
1. `NNF preserves semantics` — `equivalent (toNNF l) l evalFn`
2. `NNF is idempotent` — `toNNF (toNNF l) = toNNF l` (structural =)
3. `NNF shape invariant` — recursive check: no `Not` node except directly
   over a `Leaf`.
**Done when:** all three green over arbitrary `Lattice<int>` via the existing
generator+shrinker; seeds recorded.

## T2 · Earn the Name: Complement + Absorption Laws — Spec-ref: MANIFESTO ("bounded complemented lattice")
The repo's tagline is untested. Add:
- `Complement-⊥`: `equivalent (and' a (not a)) False`
- `Complement-⊤`: `equivalent (or' a (not a)) True`
- `Absorption`: `equivalent (and' a (or' a b)) a` and dual
- `Distributivity`: `equivalent (and' a (or' b c)) (or' (and' a b) (and' a c))` and dual
**Done when:** green in CI. If any fails, do NOT patch the law — report the
counterexample to the steward; it is a kernel finding.

## T3 · ADR-015: Semantic Optimizer (subsumption + contradiction) — Spec-ref: new ADR, draft it first
The GPT review's one gem, in-charter because it is the lattice doing lattice
work. In `Canon.Core/Lattice.fs` add `simplify : Lattice<Constraint> -> Lattice<Constraint>`:
- `And` of two `FieldBound(f, Range …)` on the same field ⇒ interval
  intersection (meet); `Or` ⇒ interval union where representable, else leave.
- Empty intersection ⇒ `False` — **contradiction detection**: an
  unsatisfiable CHECK set becomes a compile-time diagnostic surfaced by the
  CLI with the offending constraints named.
- Duplicate-leaf idempotence: `And(x,x) ⇒ x`, `Or(x,x) ⇒ x`.
Laws first: `equivalent (simplify l) l` (semantics preserved),
`simplify (simplify l) = simplify l` (idempotent), plus a unit case:
`age>18 AND age>21` simplifies to the `>21` leaf.
Write `docs/ADR-015-Semantic-Optimizer.md` (context, decision, laws) before code.
**Done when:** laws green; CLI prints a contradiction diagnostic on a crafted
fixture schema.

## T4 · Fidelity Generalized Across All Emitters — Spec-ref: GPT review §5, Transpiler.fs precedent
`Canon.Fable/Transpiler.fs` already threads `Fidelity` per node. Lift the
pattern: change `IEmitter` so every emitter returns `artifact * Fidelity`
(or a per-field fidelity map), and make `PostgresEmitter`, `OpenSearchEmitter`,
`OpenApiTranspiler` comply. `Unsupported`/`Approximate` must carry a reason
string. CLI summarizes: N exact / M approximate / K opaque per run.
**Done when:** no emitter can silently degrade — the compiler warns, as data.

## T5 · Wire Lineage.fs Into Emission — Spec-ref: GPT review "Explainability", existing Canon.Core/Lineage.fs
`Lineage.fs` exists unwired. Every emitted field carries provenance:
`derived-from: <table>.<column> via <constraint raw SQL>` — as a comment in
generated Domain.fs/TS, and as a structured sidecar (JSON) the CLI writes.
**Done when:** the tutorial's generated output visibly shows one
`CHECK(age >= 18)` provenance comment end-to-end.

## T6 · Drift Engine → Semantic Diff — Spec-ref: GPT review "Drift Engine", ADR-002 fence
Upgrade `Drift.fs` from fidelity-string comparison to comparing two canonical
models (current-introspected vs expected): per-field semantic diff using
`equivalent`/`simplify` from T3, producing `DriftViolation` records.
**Fence (ADR-002):** report-only. `FixAction` stays advisory prose; no DDL
generation, no auto-remediation. Add that sentence to the module doc comment.
**Done when:** a test shows two models differing by one constraint producing
exactly one violation with correct field attribution.

## T7 · Canonical Model Enrichment, Phase-2 Subset Only — Spec-ref: GPT review §2, cherry-picked
Add to the table model + Postgres provider + conformance suite, in this order:
foreign keys · unique constraints · composite keys · nullability · defaults.
**Explicitly deferred** (post-v1 labels, do not implement): generated columns,
identity, functional/partial indexes, domains, computed expressions,
cardinality/ownership metadata.
**Done when:** conformance suite has one test per added concept, green against
the Testcontainers fixture.

## T8 · Conformance Suite Fixture Bug — Spec-ref: prior deep review §4
The `do` block re-runs Setup/Harvest/Teardown per test and uses `let mutable`.
Convert to xUnit `IClassFixture<'T>`: harvest once, share immutably across
tests. Third parties will copy this file verbatim — it must model the
identity law.
**Done when:** one setup per class confirmed (log or timing), `mutable` gone.

## T9 · Kill the Vacuity Landmine — Spec-ref: prior deep review §5
`AgreementTests.evalConstraint` has `| _ -> true`. Replace with
`failwith $"agreement eval not implemented for {c}"` so growing the
generator without growing the evaluator breaks loudly, not vacuously.
**Done when:** compile+laws green; a deliberate temporary generator addition
demonstrates the loud failure (include in PR description, then revert).

---

## Sequencing
T0 (steward) → T1 → T2 → T3 → {T4, T5 in parallel} → T6 → T7 → T8 → T9 may
run any time after T0. T1–T3 are kernel work: if any law fails unexpectedly,
stop and escalate the shrunk counterexample — do not patch laws to pass.

## Explicit rejections from the GPT review (do not resurrect)
Oracle provider (ADR-007) · incremental compilation (no failing benchmark —
Area 6 rule) · Semantic Catalog, GraphQL, C# emitter, "AI Context" box
(post-v1 sprawl) · any new top-level module.

*One pipeline, provably correct. The reviewer said it; the constitution
already knew it. அரண்.*
