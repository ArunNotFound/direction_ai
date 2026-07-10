# EMIT.md — CanonFlow Emit Plan

> Rover rule: one panel powered, the rest carried as designs.
> **Postgres-A is the powered path.** DuckDB, JSONB, DOMAINs, facts-table,
> intent, Parquet are DESIGNED and DORMANT — each present in code as an
> honest placeholder module that compiles, states its routing rule, and
> refuses to run. A placeholder that names its own absence is documentation;
> a stub that pretends is `|| true`.

## 0. Current state (measured, honest)
- `PostgresEmitter.fs` exists and emits `CREATE TABLE … CHECK (…)` DDL —
  but it consumes `TableDef` (introspection output). It is the **round-trip
  return leg**, not greenfield emit.
- Greenfield emit (hand-authored F# refined types → DDL) exists nowhere.
- Sample analysis: **~85% of real domains route to Postgres-A** (records +
  payload-free DUs + comparison refinements). One domain (hospital-core,
  7 payload-carrying DU cases) genuinely needs DuckDB. Zero need Parquet.

## 1. The goal, falsifiable
`emit` accepts a hand-authored F# domain in the **emittable subset** and
produces deterministic Postgres DDL such that
`introspect(emit(domain)) ≅ domain` with zero loss inside the subset —
and **refuses, at emit time, with a named reason**, anything outside it.

## 2. The emittable subset (Postgres-A, the powered panel)
IN: records of primitives · `option` → NULL · payload-free DUs → `CHECK IN`
(or `CREATE TYPE … AS ENUM`, one choice, documented) · comparison refinements
(`>`, `>=`, `<`, `<=`, ranges) → `CHECK` · decimals with declared `(p,s)` ·
simple FKs · composite PKs.
OUT (refused with diagnostic): payload-carrying DUs · recursion · arbitrary
function predicates · options-of-options · units of measure · generics.
The refusal message names the type, the reason, and the dormant module that
would own it ("payload DU → see DuckDbEmitter.fs, dormant").

## 3. Laws (written before code, CI-enforced, no `|| true`)
- **L1 Round-trip:** ∀ domain in subset: `introspect(emit d) ≅ d`, loss = ∅.
- **L2 Determinism:** same domain → byte-identical DDL (canonical ordering
  of tables by dependency, columns by declaration, constraints by name).
- **L3 Total refusal:** ∀ domain outside subset: emit returns `Error` with
  a named reason — never partial DDL, never a crash.
- **L4 Deploys:** emitted DDL applies cleanly to a fresh Postgres container
  (Testcontainers in CI).

## 4. Build order (small, dense, functional tempo)
1. `EmittableDomain` DU — the subset as a *type*, so out-of-subset domains
   are unrepresentable at the boundary (parse-don't-validate, inward).
2. `PostgresAEmitter.fs` — fold over `EmittableDomain` → DDL string.
   Reuse the existing `PostgresEmitter` rendering where it fits.
3. Laws L1–L4 wired (FsCheck + Testcontainers).
4. **One greenfield sample:** `samples/greenfield-ledger/` — hand-written
   `Domain.fs` (records, one enum DU, two refinements) → emitted schema →
   introspected back → PROOF.md showing ≅. The missing headline sample.
5. README: emit row in the works/labs table flips to
   "Postgres-A: works (subset documented) · others: designed, dormant".

## 5. The dormant placeholders (the F# hangout you asked for)
One file each in `src/Canon.Emit/`, compiling, honest, refusing:

```fsharp
// DuckDbEmitter.fs — DORMANT
/// Routing rule: payload-carrying DUs (e.g. hospital-core's
/// `SubmitClaim of amount`) map faithfully to DuckDB's native UNION
/// (tagged sum type; union_tag recovers the case — tight round-trip).
/// Wakes when: a real domain with payload DUs needs persistence.
module Canon.Emit.DuckDb
let emit (_: EmittableDomain) : Result<string, EmitRefusal> =
    Error (Dormant ("DuckDB", "payload-DU domains; see EMIT.md §5"))
```

```fsharp
// JsonbEmitter.fs — DORMANT
/// Routing rule: document-shaped domains (EDI 850, GST invoice) →
/// JSONB column + structural CHECKs. Trees, not rows.
/// Wakes when: a document domain needs in-DB structural validation.
```

```fsharp
// DomainsEmitter.fs — DORMANT (Postgres CREATE DOMAIN)
/// Routing rule: reusable refinements (share_amount on many tables) →
/// named DOMAINs; preserves refinement *identity* through the round-trip
/// (the phantom tag survives as the DOMAIN name). The gold-standard leg.
/// Wakes when: L1 is green and a domain reuses refinements across tables.
```

```fsharp
// FactsTableEmitter.fs — DORMANT
/// Routing rule: already-validated facts (GSTFlow verdicts) →
/// filing-cabinet table (id, jsonb_payload, verdict, validated_at).
/// F# owns RULES; DB owns FACTS; schema emitted, never hand-authored —
/// the green→brown handoff without the deadlock.
/// Wakes when: GSTFlow persists. (Likely FIRST to wake.)
```

```fsharp
// IntentEmitter.fs — DORMANT
/// Routing rule: shops living in EF/Flyway → emit a migration *intent*
/// their tooling applies. We never manage evolution (ADR-002 stands).
```

```fsharp
// ParquetEmitter.fs — DORMANT
/// Routing rule: deeply nested PRODUCT domains for analytics → Dremel
/// record-shredding handles nesting superbly; structurally CANNOT hold
/// sum types (no level for "OR"). No current sample qualifies.
/// Wakes when: an analytical, DU-free, nested domain actually exists.
```

## 6. What this plan forbids
- Building any dormant path before its wake condition (the prettiest-panel
  trap: DuckDB unions are fascinating; that is exactly why they wait).
- Emitting partial DDL for out-of-subset types (silent loss outward).
- A README that says "emit: done". It says: subset works, rest designed.
- Touching schema evolution (EF's fortress; ADR-002).

*One panel powered and law-proven; six designs carried honestly in code,
each naming the day it wakes. That is emit, done the rover's way.*

---

# APPENDIX — Executor Briefing (for Gemini / any agent without conversation history)

Read this BEFORE §1. It replaces fifteen exchanges of context in ~15 lines.

## Context you don't have
1. CanonFlow's **introspect** direction works and is well-tested. **Greenfield
   emit does NOT exist.** The existing `PostgresEmitter.fs` consumes `TableDef`
   (introspection output) — it is the round-trip *return leg*. Do NOT extend
   it into greenfield emit. Build the new `EmittableDomain` boundary (§4.1)
   as a separate, parallel path.
2. This project's #1 recurring failure pattern is **claims outrunning
   mechanism**: a CI once ran test specimens with `|| true` (could never
   fail = enforcement theater); a commit once claimed a "launch gate
   executed" with 1 of 5 locks turned. Consequences for you:
   - Every law (L1–L4) must be able to FAIL in CI. If a test cannot fail,
     it does not count as a test.
   - Refusal (L3) is always TOTAL — `Error` with named reason. Never emit
     partial DDL with a warning. Partial output for unsupported types is
     the failure mode this whole plan exists to prevent.
   - Never mark anything done that isn't. README language is "subset works,
     rest designed" — no stronger claim, ever.
3. The **dormant modules (§5) are documentation, not TODOs.** They must
   compile and return `Error (Dormant ...)`. Do NOT implement any of them,
   regardless of how tractable or interesting they appear (DuckDB's UNION
   type especially — it is fascinating; that is precisely why it waits for
   its wake condition). Implementing a dormant module is a plan violation,
   not initiative.
4. **Style law (non-negotiable):** F# with Haskell/OCaml discipline —
   discriminated unions over interfaces, `Result` over exceptions,
   parse-don't-validate (make illegal states unrepresentable), small dense
   functions, no OOP-flavored constructs (no class hierarchies, no
   `IThing` interfaces where a DU or function type serves). If you find
   yourself writing a class, stop and model it as data + functions.
5. **The "~85% Postgres-A" claim (§0)** comes from measured analysis of the
   sample repos: most domains are records + payload-free (enum-style) DUs +
   comparison refinements. The one exception found: hospital-core has 7
   payload-carrying DU cases (`SubmitClaim of amount: decimal` etc.) —
   that is DuckDB's future job, not yours.
6. **ADR-002 (standing):** CanonFlow emits DDL; it NEVER manages schema
   evolution/migrations (diffing, ALTER generation, rollback). That is
   Flyway/EF territory. If a task seems to require migration logic, the
   task is out of scope — stop and flag it.

## Definition of done (the ONLY success criteria)
- L1–L4 green in CI against a real Postgres container (Testcontainers),
  with tests that demonstrably CAN fail (include one deliberately-broken
  case in review to prove the harness bites, then remove it).
- `samples/greenfield-ledger/` round-trips: hand-written Domain.fs →
  emitted DDL → deployed → introspected → PROOF.md showing `≅` with
  zero loss inside the subset.
- All six dormant placeholders compile and refuse with routing-rule
  doc-comments intact.
- README emit row reads: "Postgres-A: works (subset documented) · others:
  designed, dormant."
Nothing else counts as progress. More code than this is scope creep.
