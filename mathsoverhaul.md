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

**Current state (measured 2026-07-11): the law is VIOLATED.**
`VerdictEnvelope` exists twice — `GSTFlow.Core.Verification` and
`EDIFlow.Core.Verification` — and the copies have **already diverged**
(EDIFlow's encoder silently drops `Parameters`, `EffectiveFrom`,
`EffectiveUntil`, `Reference`; GSTFlow's does not). Duplication has
produced drift in under one week. `GST ∩ EDI = ∅` is currently false.
Restoring it is the sole purpose of the refactor in §7.

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
