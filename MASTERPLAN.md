# CanonFlow — Master Plan

> Schema → Proofs → Algebra → Contracts.
> A type-derivation framework where the database is the axiom set,
> refined types are the proofs, and every downstream contract is a theorem.

**License:** Apache 2.0 (project) · OSI-permissive only (dependencies: Apache 2.0 ≻ MIT ≻ BSD; no copyleft)
**Language:** F# (.NET 10) · Fable for TS emission
**Posture:** Steward-led open source. Not a product. If it helps one person, it has succeeded.

---

## 0. The Constitutional Invariant

CanonFlow is governed by one law. Everything in the repo either serves it or is deleted.

```
introspect (emit domain) ≅ domain        -- with classified loss
```

- `emit`       : Domain types → storage schema (greenfield, DDD radiating outward)
- `introspect` : Storage schema → refined domain types (brownfield, DB radiating inward)
- `≅`          : Equivalence up to `FieldClass` — every field is classified as
                 `Lossless | Widened | Narrowed | Unrepresentable`, and the loss
                 is *reported as data*, never silently absorbed.

This is a retraction pair, property-tested with FsCheck against live databases in CI.
The law is the spec. If a driver cannot state its `FieldClass` mapping, the driver
does not merge.

---

## 1. Standing Architecture Decisions (ADRs, written in Phase 0)

| ADR | Decision | Rationale (one line) |
|-----|----------|----------------------|
| 001 | **State-based DDD, not event sourcing** | The kernel is a schema↔types adjunction; a log-as-truth model gives the adjunction nothing to grab. Kafka carries *change*, not truth. |
| 002 | **No migration engine** | CanonFlow emits deterministic, versioned, idempotent DDL (`V003__orders.sql`). Flyway/dbmate/Liquibase execute and evolve. Diffing is out of scope, permanently in v1. |
| 003 | **Brownfield ships first** | Introspect is independently testable, ~40% pre-built (SqlHydra fork, Refined), and matches adoption physics: strangers have databases, not greenfield patience. |
| 004 | **One DSL** | Canon's closed six-constructor bounded complemented lattice (`True/False/Leaf/Not/And/Or`), phantom-typed `Query<'doc>`, capability-typed fields, smart-constructor normalization, planner/runner split. Per-backend planners; one algebra. |
| 005 | **Contracts via lingua franca** | F# types → JSON Schema / OpenAPI. Kotlin/Swift consume via stock generators. Native emitters live in `labs/`, explicitly experimental. |
| 006 | **Propulsion patterns, not Propulsion runtime** | FsCodec, checkpointing, ordered per-key processing, transaction-boundary grouping — adopted directly on Confluent.Kafka. No Equinox dependency. |
| 007 | **Oracle excluded from v1 drivers** | Keeps visible daylight between the project and the author's employment domain. Postgres/DuckDB/OpenSearch/SQLite only. |
| 008 | **Apache 2.0, dependency policy CI-enforced** | License scanner in the pipeline; a copyleft transitive dep fails the build, not a lawyer. |

---

## 2. Repository Layout

```
canonflow/
├── MANIFESTO.md              # thesis, non-goals (ADR-002 exclusion stated loudly)
├── LICENSE                   # Apache 2.0
├── GOVERNANCE.md             # BDFL-steward model, contribution ladder
├── spec/
│   ├── level3-spec.md        # invariants, edge cases, FieldClass taxonomy
│   └── adr/                  # MADR decision records 001–008+
├── src/
│   ├── Canon.Core/           # lattice algebra, Refined<'T,'P>, FieldClass, laws
│   ├── Canon.Ddd/            # aggregates, entities, DUs-as-invariants, VOs
│   ├── Canon.Introspect/     # schema → types  (brownfield; the first leg)
│   ├── Canon.Emit/           # types → DDL/mappings (greenfield; the second leg)
│   ├── Canon.Drivers.Postgres/
│   ├── Canon.Drivers.DuckDb/
│   ├── Canon.Drivers.OpenSearch/
│   ├── Canon.Drivers.Sqlite/
│   ├── Canon.Contracts/      # JSON Schema / OpenAPI emission
│   ├── Canon.Fable/          # shared validation predicates → TypeScript
│   └── Canon.Flow/           # Kafka/CDC arm: FsCodec, co-partitioning, SCN-gated upserts
├── labs/
│   ├── kotlin-emitter/       # experimental, may die without ceremony
│   └── swift-emitter/
├── tests/
│   ├── laws/                 # FsCheck round-trip properties (the constitution)
│   ├── drivers/              # per-driver conformance suite
│   └── contracts/            # Pact-style consumer contracts
├── examples/
│   └── thirty-minutes/       # the stranger demo, kept green in CI
└── .github/workflows/        # CI matrix + license scan
```

Provenance discipline from commit one: Conventional Commits, git trailers,
MADR links in commit bodies. The repo's own history is a lineage graph.

---

## 3. Phases and Gates

Each phase ends with a **gate** — a falsifiable condition. No gate, no next phase.

### Phase 0 — Foundation (Weeks 1–3)
- [ ] **IP clearance** — read employment agreement; if ambiguous, one written approval. **BLOCKING GATE. Nothing publishes before green.**
- [ ] Name clearance: GitHub org, NuGet, npm for `CanonFlow` / `Canon.*`
- [ ] Decide Canon-repo fate: absorb (migrate history + spec) or supersede (archive with pointer)
- [ ] MANIFESTO.md, GOVERNANCE.md, ADRs 001–008
- [ ] level3-spec.md v1: FieldClass taxonomy, lattice laws, invariant list
- [ ] License inventory + scanner wired into CI
- **Gate:** repo public, spec reviewed, CI green on an empty-but-lawful skeleton.

### Phase 1 — Kernel (Weeks 3–8)
- [ ] `Canon.Core`: lattice DSL generalized from Canon (backend-agnostic Leaf)
- [ ] `Refined<'T,'P>`: predicate algebra, applicative validation, Writer telemetry
- [ ] `FieldClass` DU + loss-classification reporting
- [ ] FsCheck law suite: lattice laws (absorption, De Morgan, complement), normalization idempotency, Refined round-trips
- **Gate:** `dotnet test` proves every algebraic law; zero drivers exist yet and that is correct.

### Phase 2 — Brownfield Introspect + Two Drivers (Weeks 8–16)
- [ ] `Canon.Introspect` core (harvest SqlHydra fork learnings; decide fork's fate)
- [ ] **Postgres driver**: `information_schema` + `pg_constraint` → Refined predicates (CHECK, NOT NULL, FK, length, range)
- [ ] **OpenSearch driver**: mappings → capability-typed fields (`IExact/IOrdered/IFullText`); proves row/document duality
- [ ] Lattice planner per driver: `Query<'doc>` → SQL WHERE / OS query DSL
- [ ] Driver conformance suite: any new driver passes it or doesn't merge
- **Gate:** the 30-minute demo works end to end against a live Postgres in CI.

### Phase 3 — Contracts Outward (Weeks 16–22)
- [ ] `Canon.Contracts`: F# types → JSON Schema (draft 2020-12) + OpenAPI 3.1
- [ ] `Canon.Fable`: refinement predicates transpiled — **the same validation runs in F# and TS**, property-tested for agreement
- [ ] Kotlin/Swift consumption documented via stock OpenAPI generators; `labs/` emitters optional
- **Gate:** a TS client rejects exactly the values the F# server rejects (cross-runtime FsCheck agreement test).

### Phase 4 — Greenfield Emit + The Law (Weeks 22–30)
- [ ] `Canon.Emit`: domain types → versioned DDL artifacts + OS mappings
- [ ] **Constitutional test goes live**: `introspect(emit(domain)) ≅ domain` with classified loss, run against real Postgres/OpenSearch containers in CI
- [ ] DuckDB + SQLite drivers (cheap now — conformance suite exists)
- **Gate:** the round-trip law passes on all four drivers. This is v1.0.

### Phase 5 — Flow (Weeks 30+)
- [ ] `Canon.Flow`: FsCodec codecs from domain types, co-partitioned topics by aggregate root, deterministic doc IDs, SCN/LSN-gated idempotent upserts, transaction-boundary grouping
- [ ] Declarative join spec shared by batch and realtime paths
- **Gate:** kill-and-resume chaos test — no duplicates, no gaps, ordering preserved per key.

---

## 4. CI Matrix

| Axis | Values |
|------|--------|
| OS | ubuntu-latest (linux is the target; windows smoke-test only) |
| .NET | 10.x |
| Databases (containers) | postgres:16, opensearch:2.x, duckdb (in-proc), sqlite (in-proc) |
| Kafka (Phase 5) | redpanda container |
| Always-on jobs | law suite · driver conformance · license scan · 30-min demo · Fable TS agreement test |

The demo is a CI job. If a stranger's first experience can break, CI knows first.

---

## 5. The 30-Minute Stranger Demo (`examples/thirty-minutes/`)

```
1. docker compose up postgres            # sample e-commerce schema, constraints included
2. dotnet canonflow introspect --pg ...  # → Domain.fs: Refined types, proofs from constraints
3. Open Domain.fs                        # CHECK (price > 0) became Refined<decimal, Positive>
4. Write one query in the lattice DSL    # phantom-typed, normalized on construction
5. dotnet canonflow contracts            # → schema.json + client.ts
6. npm test in client/                   # TS validation rejects what F# rejects — same predicates
```

If step 3 doesn't produce a small "oh." — the proof visibly derived from the
constraint — the demo has failed regardless of what else works.

---

## 6. Governance & Posture

- **Steward model** (the van Rossum posture): you direct, decide, and document; ADRs are the memory. No monetization plan, no CLA beyond DCO sign-off, no rugpulls — Apache 2.0 forever, stated in GOVERNANCE.md so sponsors and users can trust the floor.
- **Contribution ladder:** issue → driver-conformance PR → driver ownership. The conformance suite is what makes drive-by driver contributions safe.
- **AI-assisted development is disclosed** in CONTRIBUTING.md: architecture and decisions are human-owned (yours, via ADRs); generation is tooling. Honest, and it preempts provenance questions.
- **Non-goals stated as loudly as goals:** no migration engine, no ORM ambitions, no Oracle in v1, no event-store.

---

## 7. Risk Register (top five, eyes open)

| Risk | Mitigation |
|------|------------|
| Employer IP claim | Phase 0 blocking gate; personal hardware/accounts/time; Oracle excluded; domain-general framing |
| Second-system sprawl | Gates are falsifiable; anything not serving the invariant is deleted; labs/ quarantines experiments |
| Fable predicate-transpilation harder than hoped | Phase 3 gate is the agreement test; fallback is JSON-Schema-driven TS validation (still valuable, less novel) |
| OpenSearch mapping model resists FieldClass | Classified loss is *designed for this* — `Unrepresentable` is an honest answer, not a failure |
| Solo-maintainer burnout | Phases are independently shippable; v0.x after Phase 2 is already useful to a stranger |

---

## 8. Ecosystem Curation — F# Goodness, Cherry-Picked

Admission rule: a dependency enters only if it serves the constitutional invariant.
Curation means saying no; the exclusions are as deliberate as the inclusions.

| Library / Idea | Role in CanonFlow | License |
|---|---|---|
| FsCheck | The law suite — constitutional | BSD-3 |
| FsCodec | Flow codecs (Propulsion pattern, no runtime) | Apache 2.0 |
| FsToolkit.ErrorHandling | `Validation<'a,'e>` applicative — Refined's error-accumulation backbone | MIT |
| Thoth.Json | Fable-native codecs; the shared serialization substrate for the F#↔TS agreement test | MIT |
| Fantomas | Formats **emitted** `Domain.fs` — generated code people can read | Apache 2.0 |
| FSharp.Compiler.Service | Typed AST for code emission (no string templates) | MIT |
| Argu | `canonflow` CLI subcommands (provctl pattern) | MIT |
| SqlHydra (the lesson) | Build-time codegen over type providers → **ADR-009** | MIT |

**ADR-009 — Codegen, not type providers.** TPs are F#'s signature magic and the
ecosystem's quiet regret: IDE fragility, versioning pain, opaque failures.
CanonFlow generates real `.fs` files, formatted by Fantomas, checked into the
user's repo, diffable in PRs. Boring, inspectable, durable.

**Deliberately excluded:** SAFE stack (too opinionated), Elmish (irrelevant),
Paket (plain NuGet = lower stranger-friction), FAKE (plain `dotnet` + scripts),
type providers (ADR-009).

---

## 9. Small-Community Strategy — Escape Velocity for a Small Language

**Principle: sell outputs, not F#.** The F# community is thousands; the
communities CanonFlow's *outputs* serve — TypeScript, Postgres, JSON Schema,
data engineering — are millions. The public pitch is never "an F# lattice
algebra." It is:

> **Point it at your Postgres. Get TypeScript validation derived from your
> actual constraints.**

F# is the engine room; TS is the showroom. Fable is the bridge that lets a
small language's ideas be consumed by a large one. A user who never writes a
line of F# still benefits — the "helps one person" test, satisfied at scale.

Tactics, in order of leverage:

1. **Zero-friction entry:** `dotnet tool install -g canonflow`. The 30-minute
   demo requires no Ionide, no F# knowledge, no editor setup.
2. **Upstream contribution as marketing:** PRs to SqlHydra and Fable. In a
   community this small the maintainers *are* the distribution channel; one
   good PR earns more qualified attention than any launch post.
3. **Community broadcast points:** F# Advent Calendar (one post reaches the
   whole community in a day — smallness as an advantage), F# Weekly
   (Sergey Tihon), Amplifying F#, fsharp.org channels.
4. **Docs at Level-3-spec bar.** No Stack Overflow mass exists to rescue
   confused users; the docs are the community.
5. **Conformance suite as contributor onramp.** Few contributors is a given;
   each must be able to ship a driver safely without understanding the whole
   lattice. The suite makes drive-by driver PRs mergeable.
6. **Adjacent-community landings:** show CanonFlow in Postgres and TypeScript
   venues framed by *their* problems (drift between DB constraints and client
   validation), never by F# advocacy.

Success metric reframed: not "grow the F# community" but "make F#'s ideas
useful to people who will never adopt F#." That is the steward posture applied
to a language, not just a repo.

---

## 10. Agent-Assisted Development Model — The Second Secret Sauce

CanonFlow is built by a human steward directing a multi-model agent fleet
(frontier models via API, local models on personal hardware, orchestrated by
an open-source agent gateway). This is viable for one structural reason:

> **The repo's architecture makes agent labor verifiable.**
> FsCheck laws, the constitutional round-trip invariant, driver conformance
> suites, and a Level-3 spec mean agent output is never trusted — it is
> *parsed*. Parse-don't-validate, applied to labor. The gates are the parser;
> agent output is untrusted input.

### ADR-010 — Route by capability, not availability
F# is low-resource in model training data. Assignments:
- **Frontier models:** `Canon.Core`, algebra, API surface, anything touching
  the lattice or Refined internals.
- **Local / smaller models:** docs drafts, test scaffolding, CI plumbing,
  example projects, issue triage.
- All F# output passes the identity law: C#-flavored F# is a defect class,
  caught in review and (where possible) by Fantomas + analyzer rules in CI.

### ADR-011 — Agents are PR-only, never push-to-main
An always-on agent reachable via messaging gateway, running unattended crons,
is an attack surface (prompt injection via fetched pages, issue comments,
poisoned dependencies). Trust boundary = CI gates + human review. No agent
holds main-branch write credentials. No exceptions, including "just this once."

### ADR-012 — The harness is timeboxed
Orchestration tooling (gateway config, model routing, skill files) gets **one
weekend**, then is frozen. Meta-tooling attraction is a known steward failure
mode; the measure of the harness is CanonFlow commits, not harness elegance.
Harness improvements are permitted only when a concrete CanonFlow task was
blocked by a harness deficiency.

### Provenance for agent commits
Git trailers extend the existing Conventional Commits discipline:
```
Generated-by: <model-id>
Directed-by: <human>
Spec-ref: spec/level3-spec.md#section
Verified-by: laws|conformance|human-review
```
CONTRIBUTING.md discloses the model openly. Architecture and decisions are
human-owned (ADRs are the memory); generation is tooling. Legal responsibility
for every published line remains the steward's — the Phase 0 IP gate is
unchanged by who typed the code.

---

## 11. The Verification Thesis — and Its Bench

**Claim:** F# is worse for AI *generation* (low training data) but better for
AI *verification* (compiler strictness, immutability, DUs, referential
transparency), and an agentic loop's quality depends on feedback signal, not
first-draft fluency. CanonFlow's phantom types and smart constructors go
further: invalid states are *unconstructible*, so the compiler drags a
desert-trained model to correct code.

**Discipline:** this stays a hypothesis until measured. **The Banyan Bench**
(`labs/banyan-bench/`): same Level-3-specified task, same model, N attempts;
measure iterations-to-green for F#-with-laws vs C#-with-unit-tests. If the
loop advantage is real, publish the data — it would be a novel contribution
and belongs in the manifesto. If it is not real, the instrument has caught
identity-protective cognition, which is the more valuable outcome.

எண்ணித் துணிக — weigh, then act. Weighed in runs, not words.

---

## 12. Tempo — Craft-Paced, Contact-Mandatory

CanonFlow explicitly rejects sprint/output metrics. No velocity, no deadline
theater; TeX took a decade and is finished software. The weekly-week estimates
in §3 are *sequencing*, not schedule — phases may stretch freely.

The one non-negotiable is **contact with the material**: the constitution
(§0) must be exercisable — code that compiles, a law that can fail — at every
stage after Phase 1 begins. Specification without a feedback loop is the
recorded failure mode; a failing property test on an unforeseen case is the
material talking back, and no amount of planning substitutes for it.

Steward's standing self-checks (from the psychological audit, kept on record
deliberately):
1. Is this week's artifact code the material can reject, or another document?
2. Is the harness being improved instead of the kernel? (ADR-012)
3. Has "one more spec section" delayed first contact? (perpetual pre-launch
   refinement = identity protection)
4. Are new adjacent ideas being absorbed into scope? (the answer is no;
   they get an issue label `post-v1` and nothing else)

---

## 13. Risk Register — Additions

| Risk | Mitigation |
|------|------------|
| Agent-generated C#-flavored F# polluting the core | ADR-010 routing; identity-law review; analyzer rules in CI |
| Compromised/injected agent commits | ADR-011 PR-only access; CI + human review as trust boundary |
| Harness becomes the project (meta-trap) | ADR-012 timebox; harness changes require a blocked-task justification |
| Verification thesis is wrong | Banyan Bench measures it; manifesto claims only what the bench supports |
| Spec accumulation without material contact | §12 self-checks; Phase 1 gate is executable law, not prose |

---

## 14. Lineage — Ancestry, Honestly Audited

Two bloodlines. **The theory line teaches what is possible and true; the
mainstream line teaches what survives.** CanonFlow's bet: the theory line's
correctness shipped with the mainstream line's manners. A `spec/LINEAGE.md`
citing all of the below is a Phase 0 artifact — crediting ancestry earns
reviewer trust and proves we know the graveyard we build beside.

### The theory line (what is possible)
| Ancestor | What it proves / teaches | The lesson taken |
|---|---|---|
| **Baltopoulos–Borgström–Gordon 2011 (MSR)** | SQL CHECK + referential constraints → refinement types, in the F7/F# family, SMT-checked | **The founding citation.** The idea is academically validated, 15 years old, and never productized. Cite it first; our contribution is engineering, not invention |
| refined (Haskell) | Phantom-typed predicates ≈ Refined<'T,'P> | How far to go *without* an SMT solver — our answer, since .NET has no shippable solver story |
| LiquidHaskell | Solver-backed refinements at scale | Which predicate UX survives real programmers |
| Squeal / Diesel | Maximal type-level schemas | Their compile times + error messages are ADR-009's strongest evidence |
| servant | API-as-type, clients derived | Idea proven; type-level error hostility kept the audience small — codegen over cleverness |
| CUE | Types-and-constraints as a value lattice with meet | The sharpest external mirror for our normalization semantics |
| bx / lens laws (Foster–Pierce), edit lenses | `introspect ∘ emit ≅ id` is a lens law | Use their vocabulary; heed their fate — theory without a 30-minute demo dies in a PDF |
| Spivak's CQL (categorical databases) | Schema mappings as functors with adjoints | Formalizes the adjunction; adopted by almost no one — the anti-pattern of pure beauty |
| Rezoom.SQL (F#) | Type-checked SQL in F#, then stalled | The solo-maintainer cautionary tale, in our own language |

### The mainstream line (what survives)
| Ancestor | What it proves / teaches | The lesson taken |
|---|---|---|
| **jOOQ** | Schema-as-truth codegen, 15+ yrs, solo-steward economics | Never abstract the constraint away — surface it. Stewardship can sustain |
| **sqlc (Go)** | Boring deterministic codegen, massive adoption | Developers trust generated files they can read — the 30-min demo's physics |
| **Pydantic / FastAPI** | Parse-don't-validate + types→OpenAPI at mass scale | Demand is universal, not FP-niche; **the kinder UX beats the stronger type system, every time it was tried** |
| LINQ / IQueryable | Query-as-data planner/runner split, mainstream since 2007 | The one-sentence explanation of Canon planners to any .NET dev |
| EF Core reverse engineering | Brownfield scaffolding at industrial scale | Its pain points are our feature list |
| Roslyn source generators; Vogen/StronglyTypedId | The platform's own codegen-over-magic verdict; refined-value objects selling in C# | The concept sells outside FP when generated, not hand-written |
| SQLx / Rust typestate culture | Phantom types as mainstream practice | Query<'doc> is sellable to ordinary engineers **iff errors are humane** |
| PostgREST / Hasura / Supabase | "Point it at your database" builds ecosystems | The adoption thesis, proven thrice — they derive APIs; nobody derives proofs |
| Hibernate/JPA | Annotation magic, runtime surprise | The anti-ancestor we define against |
| SQLDelight; Ent; TypeSpec | Codegen ergonomics; schema-as-code; emitter architecture | Study for Canon.Emit / Canon.Contracts mechanics |

## 15. Position — The Gap, Audited

**The revised novelty claim (write exactly this, no more):** every organ of
CanonFlow exists somewhere; the organism does not. Nobody ships
(1) constraint-derived refined types as first-class proofs [idea: MSR 2011;
product: absent], (2) a four-way classified-loss taxonomy reported as data
[bx literature gestures; no tool ships], (3) identical predicates executing
in F# and TypeScript, property-tested for agreement [Zod-style ecosystems
duplicate; none transpile the proof]. The contribution is composition and
productization — the right kind of novelty for a steward: no new theory to
defend, every component de-risked by an ancestor.

**Why the gap exists (all three, kept honest):** the required intersection
(billion-row constraint scars × refinement theory × codegen ergonomics) is
freakishly rare; economic gravity pulled capable builders to the API layer
where demos raise money; and the market has not yet priced proofs — which is
a risk, not only an opportunity.

**The Wlaschin objection, answered in the architecture:** enterprise =
maintainability by rotating average teams, which penalizes abstraction one
clever person writes. CanonFlow never asks the maintainer to *write* refined
types — it generates them, Fantomas-clean, and radiates plain TS/JSON Schema
outward. The rotating team lives in the emitted edges; the algebra is a
frozen core. **Haskell asks the enterprise to climb the banyan; CanonFlow
drops the aerial roots.** Their purity is our moat.

**Competitive posture:** the head start is measured in nobody-noticed-yet.
Prisma-class players could ship a shallow "constraint-derived validators"
feature in a quarter — without lattice, loss, or law, but capturing the
narrative. The TS FP/validation ecosystem (Zod lineage) is the closest
cultural rival: one runtime, hand-written schemas, database-as-axioms
unclaimed. Defense = speed to the demo + the law as the moat imitators skip.
README line one preempts the "Flow = orchestrator?" confusion.

**Learnings log (this planning cycle):** the falsification search *worked* —
"nobody has done this" survived six ecosystems then partially fell to a 2011
paper; permanent rule: **every novelty claim gets a falsification search
before it enters a public document, and finding prior art is a win** (it
validates the idea and sharpens the claim). Second learning: curating only
prestige ancestry (Haskell) while omitting adoption ancestry (Java/Go/Python)
was identity-protective curation — mirrored by the steward's own reviewer.
Both columns, always.
