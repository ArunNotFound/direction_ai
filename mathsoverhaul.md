# CANON.md — The Trinity Constitution

> *Law → Type → Function → Proof → Implementation.*
> Implementation is last, not first. Every abstraction justifies itself with
> a law, a type, and a transformation. When those three align, the code
> shortens, the architecture clarifies, and neither humans nor AI have room
> for ambiguity.

---

## 0 · PRIME LAW

```
∀K :  Flow(K) = CF ⊕ K
```

Everything follows. If a proposed feature cannot be expressed in terms of
this equation, it belongs elsewhere — or should not exist.

```
CF        = Engine        (reasoning: verify, prove, evidence)
K         = Knowledge     (a domain: GST, EDI, …)
Flow(K)   = CF ⊕ K        (a product)

GSTFlow   = Flow(GST)
EDIFlow   = Flow(EDI)
```

---

## 1 · CONSTITUTION

```
∀x :  Generic(x)  ⇒  x ∈ CF
      Domain(x)   ⇒  x ∈ Flow(K)
```

**Ownership**

```
CanonFlow = Reasoning
GSTFlow   = GST Knowledge
EDIFlow   = EDI Knowledge
```

---

## 2 · THE REFACTOR LAW

```
GST ∩ EDI  = ∅            (domains never touch)
GST ∩ CF   = Engine
EDI ∩ CF   = Engine

∴ GST = CF ⊕ GST
  EDI = CF ⊕ EDI
```

**Historical state (measured 2026-07-11): the law was VIOLATED.**
`VerdictEnvelope` existed twice — `GSTFlow.Core.Verification` and
`EDIFlow.Core.Verification` — and the copies had **already diverged**
(EDIFlow's encoder silently dropped `Parameters`, `EffectiveFrom`,
`EffectiveUntil`, `Reference`; GSTFlow's did not). Duplication had
produced drift in under one week. `GST ∩ EDI = ∅` was false.
Restoring it was the sole purpose of the refactor in §7.

**Current state (measured 2026-07-12): the law is RESTORED.**
- Verification extraction is complete (`CanonFlow.Core` contains the pure reasoning kernel).
- GSTFlow and EDIFlow package migration is complete (both reference `CanonFlow.Core.0.1.0-alpha` via NuGet, relative sibling paths eradicated).
- Constitutional conformance continues (Falsifier logs, Contact proof).

---

## 3 · EXTRACTION RULE

```
if  UsedBy(GST) ∧ UsedBy(EDI) ∧ DomainAgnostic
      ⇒ MoveTo(CF)
else  ⇒ RemainIn(Domain)
```

**Required in CF** (the reasoning kernel — nothing else):

```
Verdict · Evidence · Rule · Verify · Proof
Hash · CanonicalJson · RuleMetadata
```

**Forbidden in CF** (violations, not preferences):

```
GST ∈ CF           ✗
EDI ∈ CF           ✗
PartnerRules ∈ CF  ✗
```

---

## 4 · MORPHISMS, NOT CLASSES

```
OO:  Class owns behaviour
F#:  Behaviour transforms values
```

Never `InvoiceValidator`. Always:

```
verify : Knowledge → Document → Verdict
```

The pipeline is an arrow chain:

```
Document → Canonical → Verify → Proof → Verdict
```

---

## 5 · THE DELETION PROOF  (how genericity is *proven*, not claimed)

```
delete(CF)   ⇒  GST ✗ , EDI ✗        (CF is load-bearing)
delete(GST)  ⇒  CF ✓ , EDI ✓         (GST is a leaf)
delete(EDI)  ⇒  CF ✓ , GST ✓         (EDI is a leaf)
```

A claim of genericity that fails this test is decoration.
Run it mentally before every promotion to CF.

---

## 6 · CONSERVATION LAWS

**Entropy.** `H = Duplication + Exceptions + SpecialCases`

```
∀ PR :  ΔH ≤ 0
```

No change may increase architectural entropy.

**Cost.**

```
before:  Δ = ΔCF + ΔGST + ΔEDI        (fix everything three times)
after:   Δ = ΔCF                       (fix once)

before:  T = TCF + TGST + TEDI         (AI context: all three)
after:   T = TCF + TK ,  K ∈ {GST, EDI}
```

**Objective.**

```
min { Duplication, Context, Tokens, SurfaceArea, Coupling }
subject to:   Tests = Green
              Contact ≥ 1          ← THE BINDING CONSTRAINT
```

`Contact ≥ 1` means: at least one real human, not the author, has used the
product and confirmed the verdict was right. **This constraint is currently
UNSATISFIED, and it dominates every other term.** A mathematically perfect
trinity with `Contact = 0` is a sealed room. Optimising the other terms while
this one is violated is not engineering; it is decoration under a proof.

---

## 7 · THE BOUNDED REFACTOR  (exactly three moves — no more)

```
1.  create  Canon.Verification ∈ CF
      contents: RuleOutcome · Evidence · RuleMetadata · RuleResult
                VerdictEnvelope · canonicalJson · aggregate · sha256
      purity:   ¬Postgres ∧ ¬TableDef ∧ ¬GST ∧ ¬EDI ∧ ¬Fable-specific
      source of truth: GSTFlow's encoder (the CORRECT twin — complete)

2.  GSTFlow, EDIFlow  →  reference Canon.Verification
    delete both local copies.  Namespace change only.

3.  Agreement tests green on both sides.
    (GSTFlow's red-run-proven suite is the net. EDIFlow's garbage-fuzz
     property comes along: Engine never crashes on arbitrary input.)
```

**STOP.** Forbidden inside this refactor:

```
✗ renaming sprees            ✗ Rule<'K> generalisation crusade
✗ restructuring working code ✗ features smuggled as refactor
✗ new abstractions "while we're in there"
```

Brutal = **minimal and total**, never *large*. Budget: one session.
The algebra *describes* the code; the refactor's only job is to make the
description TRUE where §2 says it is false.

---

## 8 · THE METHOD  (per feature, per PR, forever)

```
1  Find the invariant.     What must always hold?
2  Find the algebra.       What composes with what?
3  Types become maths.     Illegal states unrepresentable.
4  Functions transform.    Arrows, not owners.
5  Prove.                  A test that can FAIL. A red run, witnessed.
6  Implement.              Last.
```

**Weekly reduction question:** *what can be removed?*

```
Complexity ↓  ⇒  Elegance ↑
```

---

## 9 · MISSION

```
Min(Code) · Min(Duplication) · Min(Context)
Max(Verification) · Max(Determinism) · Max(Reuse) · Max(Trust)

Max(Trust)  requires  Contact ≥ 1
```

Trust is not proven by a lattice. It is proven by a stranger who ran your
tool on their own document and found the verdict right.

**The whole algebra evaluates to one unfinished term.**
Every equation here is satisfiable by code except the last, and the last
one dominates. Solve it first.

---

## 10 · THE UPDATE LAW  (compiled engine ⊕ signed packs)

### 10.1 · The apparent problem
```
Gazette(t) : rate(HSN_x) : 12% → 18%   at midnight
Engine ∈ {AOT, Wasm}                    ⇒ compiled
∴ naive conclusion:  rule change ⇒ recompile + retest + redeploy
```
The tempting answer is a dynamic BRE fetching rules from an API at runtime.
**That answer is forbidden**, and not for purity — for correctness:

```
BRE:      rules(t) mutable at runtime
∴         replay(doc, t₀) ≠ replay(doc, t₁)
∴         REPLAY LAW BROKEN
```
A verdict that cannot be reproduced years later is worthless in a dispute.
Determinism is not a cost of compilation — determinism *is* compilation.

### 10.2 · The decomposition (the actual answer)
```
Rule  =  Law ⊗ Params

Law     : structural, algebraic, changes ~never
          (checksum algebra · tax-split invariant · sum-consistency ·
           envelope integrity · document-class semantics)
Params  : tabular, dated, changes often
          (rate tables · HSN→rate maps · state codes · effective dates ·
           ISO-4217 · UNCL5305 · UOM lists · IRP public keys)

∴  K  =  K_law  ⊕  K_params

    K_law    ⊂  binary   (compiled: AOT + Wasm)
    K_params ⊂  pack     (signed data file, loaded at runtime)
```
**A rate change 12%→18% is a Params change, not a Law change.**
It requires a new pack. It does NOT require recompilation.

### 10.3 · The Pack Law
```
Pack  =  ⟨ id, version, effectiveFrom, data, signature ⟩

∀ pack :  Data(pack) ∧ ¬Code(pack)          ← packs are DATA, never CODE
          Verify_Ed25519(pack) ∨ Reject(pack)
          ¬Network(load)                     ← packs arrive as FILES
```
Distribution = human channels (a CA downloads once, forwards to fifty
clients on WhatsApp; a USB stick; an app-store update). The engine never
calls a server. The signature is the trust; the file is the transport.

**Forbidden inside a pack** (this is the BRE line, and it is absolute):
```
✗ executable logic    ✗ scripting/DSL    ✗ arbitrary predicates
✗ anything Turing-complete
```
A pack that can *compute* is a remote-code-execution channel into an
offline trust product. Packs declare facts; the compiled Law evaluates them.

### 10.4 · The Pinned-Context Determinism Law  (supersedes naïve replay)
```
verify :  Document → EngineVersion → PackVersion → EffectiveDate → Verdict

∀ (d, e, p, t) :  verify(d, e, p, t)  is constant  ∀ wall-clock
```
The envelope RECORDS all four. Replay is exact forever, *because* the
parameters were pinned rather than fetched.

```
GST:  pack = { rates, HSN map, state codes, IRP keys }
EDI:  pack = { ISO-4217, UNCL5305, UOM, profile version, schematron digest }
```
Identical law, two domains — therefore `PackLoader ∈ CF`, `PackContents ∈ K`.
(Extraction Rule §3 applies: the loader is generic; the tables are domain.)

### 10.5 · Staleness Honesty
```
∀ verdict :  emit(PackVersion, PackEffectiveFrom)
             stale(pack, now)  ⇒  Warn("rules as of {date}; a newer pack may exist")
```
The engine never pretends currency it cannot verify. An offline tool that
*says* how fresh it is beats an online tool that silently changes under you.

### 10.6 · What DOES require recompilation (stated honestly)
```
new rule TYPE          ⇒ recompile      (a new law)
new document CLASS     ⇒ recompile      (new semantics)
changed algebra        ⇒ recompile      (rare; e.g. rounding statute rewrite)
rate/code/date change  ⇒ PACK ONLY      ← the 95% case
```
The honest claim is not "never recompile." It is:
**"parametric change never recompiles; structural change does — and structural
change is rare, reviewable, and testable."**

### 10.7 · Cost
```
before:  Δrate  =  recompile + retest + redeploy(all modes)
after:   Δrate  =  sign(pack) + distribute(file)

before:  ΔH increases with every hardcoded table
after:   ΔH ≤ 0        (tables leave the code; the code keeps only laws)
```
The Update Law therefore *satisfies* the Entropy Law §6, rather than
violating it. Hardcoded rates were entropy. Packs are the reduction.

---

## 11 · THE INTAKE LAW  (external contracts → CanonicalIR)

### 11.1 · The third arrow
```
introspect : DB       → IR        (exists, proven)
emit       : IR       → Targets   (Postgres-A powered)
intake     : Contract → IR        (this law)

I : S → A
S = { JSONSchema, OpenAPI, JSON-sample }          ← embraced, in order
S̄ = { d.ts, XSD }                                 ← deferred, wake-conditioned
A = F ⊕ J ⊕ K ⊕ T ⊕ R
    F# Types ⊕ Thoth Codecs ⊕ Adapter Skeleton ⊕ Contract Tests ⊕ Fidelity Report
```

### 11.2 · Trust Law  (the differentiator — this is not quicktype)
```
Generator ≠ Proof
TrustedAdapter = GeneratedAdapter ⊕ ContractTests ⊕ FidelityReport
```
Prior art (openapi-generator, quicktype, FSharp.Data) ships code and hope.
CanonIntake ships code, the tests that hold it, and the honest account of
what it could not know. Also: type providers ∉ Fable — source-file emission
serves the dual-runtime law where compile-time magic cannot.

### 11.3 · Uncertainty Law
```
UnknownSemantics(s) > 0  ⇒  Fidelity(I(s)) ≠ Exact

JSONSchema : Fidelity may reach Exact      (constraints are declared)
OpenAPI    : mostly Exact                  (JSONSchema inside)
JSON-sample: Unknown-tinted by construction (a sample is not a schema)
```
Schema constraints (min/max/pattern/enum/required) lift into the lattice as
refinements — intake IS introspect-for-documents.

### 11.4 · Boundary & Round-trip Laws
```
ExternalContract → RawType → Normalize → CanonicalIR → Verdict
decode(encode(x)) ≅ x                      (law-tested per adapter)
∀ generated line : Provenance(line) ≠ ∅    (cites source schema path)
```

### 11.5 · Placement  (§3 applies)
```
IntakeEngine ∈ CF          (generic: schema → lattice lifting)
AdapterBody  ∈ Flow(K)     (domain owns the meaning; skeletons rot —
                            generate the interface + tests, humans own flesh)
```

### 11.6 · First falsifier  (internal, conservative, this month)
```
INV-01 (official GSTN e-invoice JSON Schema)
   → I(INV-01) → RawInvoice'
diff(RawInvoice', RawInvoice_handwritten)
   = bugs ∨ documented deviations          (both are wins)
```
CanonIntake's first consumer is GSTFlow itself. No new product surface.

### 11.7 · Deferred set, on the record
```
d.ts : wakes on a real user request        (semantically thin, big parse lift)
XSD  : wakes with EDIFlow unfreeze          (UBL is XSD-defined — EDIFlow.Ubl
                                             could be GENERATED; subset-XSD only)
```

---

## 12 · THE DEPENDENCY LAW  (final sweep before freeze)

### 12.1 · The razor
```
∀ lib ∈ VerdictPath :  Fable(lib) ∧ NativeAOT(lib) ∧ ¬Network(lib)
∀ lib anywhere     :  ΔH(lib) ≤ 0     (must remove more complexity than it adds)
```

### 12.2 · The allowlist (vetted, frozen)
```
CORE (verdict path — dual-target):
  Thoth.Json                 serialization law (only dual-target option)
  FsToolkit.ErrorHandling    result/validation CEs — Fable ✓, zero-cost
  FSharp.UMX                 erased units for primitives — string<gstin>,
                             decimal<inr>; types-as-proofs at zero cost
TEST:
  FsCheck                    the proof engine (laws = properties)
CLI PRESENTATION ONLY (never oracle output):
  Spectre.Console            human-rendered tables for the CA demo;
                             canonical JSON untouched
DEV TOOLING:
  Fantomas                   style law, mechanically enforced in CI
```

### 12.3 · The refusals (recorded with reasons)
```
FParsec        ✗ ∉ Fable — breaks One-Engine at the parser. X12 parsing =
                 hand-rolled ~100-line combinators in shared core.
Validus        ✗ second validation vocabulary; RuleResult is richer (ΔH > 0)
NodaTime       ✗ ∉ Fable; civil dates need DateOnly + ISO only.
                 (Fiscal-year Apr–Mar = 20-line domain module, not a dep.)
FSharpPlus     ✗ abstraction without proof; plain F# suffices
FSharp.Data    ✗ type providers ∉ Fable (already ruled, §11.2)
FsLexYacc      ✗ generator overkill; ∉ Fable
Argu           ~ philosophically perfect, reflection-based (AOT risk);
                 hand-rolled args stay; revisit if CLI verbs multiply
Expecto        ~ nicer than xunit; migration = ΔH > 0 for aesthetics; stay
```

### 12.4 · Freeze
The trinity dependency surface is now CLOSED. Any future addition must pass
§12.1 and be recorded here with its proof. The strongest finding of the
final sweep: FSharp.UMX — the philosophy of this project available as an
erased type, for free. The strongest confirmation: everything essential was
already in hand. The ecosystem check found two gems and no missing pillar —
the trinity was not reinventing the world; it was refusing the parts of the
world that break its laws.
