# EMIT-FINAL.md — CanonFlow Emit Plan (self-contained, executor-ready)

> This document travels alone. Full plan + all amendments + executor
> briefing. No conversation history required.
> Principle: **one emit path powered (Postgres-A); six alternatives designed
> and dormant** — present in code as honest placeholders that compile, state
> their routing rule, and refuse to run.

---

# PART A — EXECUTOR BRIEFING (read first; binding)

## A1. Context you don't have
1. CanonFlow's **introspect** direction (Postgres → refined F# types +
   validators) works and is tested. **Greenfield emit does NOT exist.**
   `src/Canon.Emit/PostgresEmitter.fs` consumes `TableDef` (introspection
   output) — it is the round-trip *return leg*. **Do NOT extend it.**
   Build the new `EmittableDomain` boundary (B4) as a separate path.
2. This project's #1 recurring failure pattern is **claims outrunning
   mechanism** (historically: CI specimens run with `|| true` so they could
   never fail; a "launch gate executed" commit with 1 of 5 locks turned).
   Binding consequences:
   - Every law must be able to FAIL in CI. A test that cannot fail does
     not count as a test.
   - Refusal is always TOTAL — `Error` with a named reason. **Never emit
     partial DDL** for unsupported types, with or without warnings.
   - Nothing is labeled done that isn't. README language: "subset works,
     rest designed" — never stronger.
3. **Dormant modules (B7) are documentation, not TODOs.** They compile and
   return `Error (Dormant ...)`. Do NOT implement any of them, regardless
   of tractability or interest (DuckDB's UNION especially — fascinating is
   precisely why it waits). Implementing a dormant module is a plan
   violation, not initiative.
4. **Style law:** F# with Haskell/OCaml discipline — DUs over interfaces;
   `Result` over exceptions; parse-don't-validate; small dense functions;
   no class hierarchies or `IThing` interfaces where a DU or function type
   serves. Writing a class? Stop; model as data + functions.
5. **Scope fence (standing ADR):** CanonFlow emits DDL; it NEVER manages
   schema evolution (diff, ALTER, rollback) — Flyway/EF territory. If a
   task seems to need migration logic, it is out of scope: stop and flag.
6. **Routing-claim provenance:** measured sample analysis → most domains
   are records + payload-free DUs + comparison refinements → ~85% route to
   plain Postgres. Exception: hospital-core, 7 payload DU cases → see A1.7.
7. **Behavioral vs state:** payload DU cases shaped like commands/events
   (`SubmitClaim of amount`, `PatientRegistered of id`) are **behavioral
   declarations, not persistent state** — they belong in an event/facts
   store, not a relational schema. B3's diagnostics distinguish the two.

## A2. Definition of done (the ONLY success criteria)
- Laws L1–L5 green in CI on a real Postgres container (Testcontainers),
  harness proven to bite: ONE deliberately-broken case shown failing in
  the review PR, then removed.
- `samples/greenfield-ledger/` round-trips: hand-written Domain.fs →
  emitted DDL → deployed → introspected → PROOF.md showing logical
  equivalence, zero loss inside subset.
- `canonflow analyze` prints the B5 report for greenfield-ledger.
- All six dormant placeholders compile and refuse, doc-comments intact.
- README emit row reads exactly: "Postgres-A: works (subset documented) ·
  others: designed, dormant."
**Nothing else counts as progress. More code than this is scope creep.**

---

# PART B — THE PLAN

## B1. Goal (falsifiable)
`emit` accepts a hand-authored F# domain in the emittable subset (B2) and
produces deterministic Postgres DDL such that `introspect(emit(domain))`
is **LogicallyEquivalent** to `domain`: identical normalized constraint
expression trees per field, identical nullability, identical enum value
sets, identical primary/foreign keys. Equality of meaning (normalized
trees), never of SQL text. Outside the subset: total refusal (B3).
(ONE equivalence level only; finer gradations wait for a second target.)

## B2. The emittable subset (Postgres-A — the powered path)
IN:
- records of primitive fields
- `option` → nullable column
- payload-free DUs → `CREATE TYPE … AS ENUM` (the single documented choice)
- comparison refinements (>, >=, <, <=, two-bound ranges) → CHECK
- decimals with declared precision/scale → NUMERIC(p,s)
- simple FKs; composite PKs
OUT (refused with named diagnostic):
- payload-carrying DUs (two-way diagnostic, B3)
- recursive types · arbitrary function predicates · option-of-option ·
  units of measure · generics

## B3. Refusal diagnostics (exact behavior)
Refusals name the type, the reason, and the dormant owner of the future:
- payload DU that is domain **state** →
  Error (OutOfSubset (t, "payload union (state); faithful target is DuckDB
  native UNION — see DuckDbEmitter.fs (dormant)"))
- payload DU shaped as **command/event** (verb/past-tense cases) →
  Error (OutOfSubset (t, "behavioral declaration; belongs in an event/facts
  store — see FactsTableEmitter.fs (dormant)"))
- recursion, function predicates, etc. → analogous named reasons.
Collect ALL offenders (do not stop at first). Zero DDL on any refusal.

## B4. Build order (in sequence)
1. `EmittableDomain` — the subset as a TYPE (a DU); construction from a
   general model returns Result<EmittableDomain, EmitRefusal list>.
2. `analyze` — the B5 report (the step-1 classifier + counting + reasons).
3. `PostgresAEmitter` — fold over EmittableDomain → DDL. May reuse
   rendering fragments from PostgresEmitter.fs; must NOT take TableDef input.
4. Laws L1–L5 in CI; prove the harness bites (A2); land.
5. `samples/greenfield-ledger/` — Domain.fs: 2–3 records, one payload-free
   DU, two comparison refinements, one FK → emit → deploy → introspect →
   PROOF.md with equivalence evidence.
6. README emit row updated to the exact A2 sentence.

## B5. `canonflow analyze` (a report, not a router)
Prints measured structure + routing consequence + reasons. NO confidence
scores, NO ranking, NO `route` command — one powered target needs a
report, not a router.
```
greenfield-ledger: 3 products, 1 enum DU (3 cases), 2 refinements, 1 FK
  → Postgres-A (in subset)
hospital-core: 7 payload DU cases, all command/event-shaped
  → OUT of subset: behavioral; event/facts store (dormant) — see B3
```

## B6. Laws (CI-enforced; each must be able to fail)
- **L1 Round-trip:** ∀ d in subset: introspect(emit d) LogicallyEquivalent
  to d, loss = ∅.
- **L2 Determinism:** same domain → byte-identical DDL. Canonical order:
  tables by FK-dependency then name; columns by declaration; constraints
  by deterministic generated name.
- **L3 Total refusal:** ∀ d outside subset: Error with all offenders
  named, zero DDL.
- **L4 Deploys:** DDL applies cleanly to a fresh Postgres container in CI.
- **L5 Trees, not text:** once a constraint is an expression tree, no
  stage re-parses rendered SQL. Trees in, trees compared, SQL only at
  final render.

## B7. Dormant placeholders (one file each in src/Canon.Emit/)
Each compiles, carries its routing rule as doc-comments, refuses:

```fsharp
// DuckDbEmitter.fs — DORMANT
/// Routing: payload-carrying DUs that are genuine STATE map faithfully to
/// DuckDB's native UNION (tagged sum; union_tag recovers the case — tight
/// round-trip). Postgres cannot hold these without lossy encodings.
/// Wakes when: a real domain with payload-DU state needs persistence.
module Canon.Emit.DuckDb
let emit (_: EmittableDomain) : Result<string, EmitRefusal> =
    Error (Dormant ("DuckDB", "payload-DU state domains; EMIT-FINAL B7"))
```

```fsharp
// FactsTableEmitter.fs — DORMANT  (likely FIRST to wake: GSTFlow persistence)
/// Routing: already-validated facts (e.g. GSTFlow verdicts) → filing-
/// cabinet table (id, jsonb_payload, verdict, validated_at). F# owns
/// RULES; DB owns FACTS; schema emitted, never hand-authored — greenfield
/// persistence without dual sources of truth.
/// Each stored verdict carries the evidence envelope:
///   input hash · engine version · rule-pack version · rule effective date
///   rules executed · facts derived · verdicts · unsupported checks ·
///   output hash
/// Defensible claim (exact wording): "proves that its supported, versioned
/// rules were consistently evaluated against the supplied data" — never
/// "GST-law compliant".
```

```fsharp
// JsonbEmitter.fs — DORMANT
/// Routing: document-shaped domains (EDI transactions, invoices) → JSONB
/// column + structural CHECKs. Trees, not rows.
```

```fsharp
// DomainsEmitter.fs — DORMANT (Postgres CREATE DOMAIN)
/// Routing: refinements REUSED across tables → named DOMAINs; preserves
/// refinement identity through the round-trip (phantom tag survives as
/// the DOMAIN name). Gold-standard round-trip leg.
/// Wakes when: L1 green AND a domain reuses refinements across tables.
```

```fsharp
// IntentEmitter.fs — DORMANT
/// Routing: shops living in EF/Flyway → emit a migration *intent* their
/// tooling applies. CanonFlow never manages evolution (A1.5).
```

```fsharp
// ParquetEmitter.fs — DORMANT
/// Routing: deeply nested PRODUCT domains for analytics → Dremel record-
/// shredding (repetition/definition levels) is superb at nesting and
/// structurally CANNOT hold sum types (no level for "OR").
/// Wakes when: an analytical, DU-free, deeply nested domain exists.
/// None currently does.
```

## B8. Governance rules (zero-cost, binding)
- **Routing fork:** every enhancement asks "general or domain-specific?"
  General → CanonFlow. Specific → the domain app (e.g. GSTFlow).
- **Kernel-entry rule:** a discovery enters the kernel only when it
  (a) appears in ≥2 domains, (b) has a clear abstraction, (c) has specified
  fidelity, (d) has tests + counterexamples. Else: sample-local adapter.
- **DISCOVERIES.md per sample:** unsupported rule classes, awkward
  representations, reasons-not-to-generalise-yet.
- **Issue classification:** general defect → CanonFlow · missing general
  abstraction → experimental proposal · domain rule/UX → domain app ·
  emitter bug → that emitter. Effort: 80% domain product, 20% core.

## B9. Refusals ledger (rejected; recorded so they stay out)
- Canon IR as a published versioned spec with migration rules — premature
  before the first external user; freezing philosophy ≠ publishing a spec.
- Multiple fidelity/equivalence levels — one target needs one.
- Capability-matching engine, `canonflow route`, confidence scores —
  wake with target #2; the B5 report suffices today.
- AI sidecar as a query SERVICE — pull-model rejected; push artifacts
  (generated files with provenance comments) already carry the value at
  zero runtime cost.
- Any migration/evolution feature — permanent fence (A1.5).

*One panel powered and law-proven; six designs carried honestly in code,
each naming the day it wakes. Dense, refusing, deterministic.*
