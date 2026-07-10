# CanonFlow Enhancements and Strategic Direction

> Status: Proposed roadmap and architectural consolidation note  
> Scope: CanonFlow core, CanonFlow.Samples, and GSTFlow as the first serious domain product

## 1. Purpose

CanonFlow has evolved beyond a database-to-code generator. Its strongest emerging identity is a **semantic compiler and knowledge layer** that carries authoritative meaning across databases, application types, contracts, validators, metadata, proof reports, and AI-agent workflows.

This document records the essential enhancements needed to stabilise that direction without freezing experimentation too early.

The recommended strategy is:

```text
CanonFlow
  -> stabilise the semantic foundation
  -> use GSTFlow as the first serious customer
  -> feed only generalised discoveries back into CanonFlow
  -> keep GST-specific rules inside GSTFlow
```

CanonFlow should not be treated as finished. It should be treated as mature enough to support one focused product while both evolve through disciplined feedback.

---

## 2. Expected End State

When the enhancements below are complete, CanonFlow should provide:

```text
Source model or schema
  -> Canon IR
  -> semantic analysis
  -> target capability matching
  -> explainable emit plan
  -> generated artifacts
  -> fidelity and proof report
  -> drift and impact analysis
  -> AI-readable semantic queries
```

The goal is not merely to generate more files. The goal is to make important rules:

- explicit;
- portable;
- explainable;
- versioned;
- traceable;
- target-aware;
- verifiable;
- consumable by humans and AI agents.

---

## 3. Essential Core Enhancements

## 3.1 Canon IR Specification

Canon IR must become a formal, versioned specification rather than an implicit collection of internal types.

Minimum stable forms:

```text
Scalar
Product
Sum
Optional
Collection
Reference
Refinement
Constraint
Expression
Provenance
Behavioral declaration
Opaque semantic node
```

Required properties:

- independent of F#, SQL, TypeScript, or any target language;
- serialisable to a stable format;
- versioned with migration rules;
- capable of preserving unknown semantics as opaque rather than discarding them;
- usable by all introspectors, emitters, proof engines, and agent interfaces.

### Acceptance criteria

- Every supported source produces Canon IR.
- Every emitter consumes Canon IR only.
- No emitter depends directly on provider-specific source models.
- IR format has a published schema and version number.

---

## 3.2 Stable Expression and Constraint Tree

Constraints must not be stored primarily as raw strings.

Required expression forms include:

```text
Field
Literal
Compare
And
Or
Not
In
Length
IsNull
Function
RelativeFieldBound
OpaqueExpression
```

Example:

```text
age >= 18 AND age < 120
```

must become a structured expression tree so CanonFlow can:

- simplify it;
- compare it semantically;
- emit it differently for each target;
- explain it;
- prove supported projections;
- detect drift.

### Acceptance criteria

- No supported constraint requires string parsing after entry into Canon IR.
- Unsupported source expressions remain traceable as `OpaqueExpression`.
- Normalisation produces a canonical expression form.

---

## 3.3 Formal Fidelity Model

Every projection must report what was preserved and what was lost.

Recommended fidelity levels:

```text
ExactSemantic
ExactStructural
LosslessButReencoded
Approximate
DocumentaryOnly
Unsupported
Opaque
```

Fidelity is target-specific. A composite foreign key may be exact in PostgreSQL, documentary in OpenAPI, and non-local in TypeScript.

### Acceptance criteria

Every generated artifact includes:

- source semantic node;
- target representation;
- fidelity classification;
- explanation;
- unsupported semantics;
- provenance;
- verification evidence where available.

---

## 3.4 Provenance

Every IR node should retain where it came from.

Recommended provenance fields:

```text
source language/provider
source file or object
source type/table
source member/column
source span when available
introspection version
rule-pack version
```

Provenance enables:

- precise diagnostics;
- explainability;
- proof reports;
- impact analysis;
- generated comments;
- reliable AI-agent answers.

### Acceptance criteria

A user can ask:

> Why does this target constraint exist?

and receive a trace from generated artifact back to the original F# field, SQL constraint, or domain rule.

---

## 3.5 Target Capability Model

Target selection must not be hard-coded as simplistic rules such as:

```text
payload DU -> DuckDB
```

Each target needs a declared capability profile.

Example dimensions:

```text
products
payload-free enums
payload-carrying unions
named domains
nested collections
recursive values
relational constraints
cross-field constraints
temporal rules
document storage
transactional suitability
analytical suitability
```

Example target strategies:

```text
PostgresRelational
PostgresDomains
PostgresJsonb
PostgresNormalisedUnion
DuckDbUnion
ParquetNested
FactsTable
```

### Acceptance criteria

- Targets publish machine-readable capabilities.
- Emit routing is produced by capability matching, not scattered conditional logic.
- New targets can be added without rewriting the semantic core.

---

## 3.6 Structural Metrics and Domain Analysis

CanonFlow should measure the source model before routing.

Recommended metrics:

```text
record/product count
sum/DU count
enum-only DU count
payload-carrying DU count
heterogeneous payload count
optional count
collection count
maximum collection depth
recursive type count
named refinement count
cross-field constraint count
opaque rule count
relationship density
```

Metrics should support explanation, not merely scoring.

### Acceptance criteria

`canonflow analyze` emits a stable report showing measured structure and why a target strategy was recommended.

---

## 3.7 Target-Aware Emit Planning

Emission should be a two-stage process:

```text
Analyze
  -> Recommend
  -> Warn
  -> User confirms or overrides
  -> Emit
```

A routing decision should contain:

```text
recommended target
alternative targets
confidence
reasons
warnings
expected fidelity
operational caveats
```

Important principle:

```text
type shape
+ semantic fidelity
+ workload
+ operational requirements
+ deployment constraints
= emit recommendation
```

F# shape alone must never choose the database.

### Acceptance criteria

The router can distinguish:

- best structural representation;
- best transactional deployment;
- best analytical representation;
- fidelity trade-offs.

---

## 3.8 Verification Engine

The constitutional invariant must become executable and precisely defined:

```text
introspect(emit(domain)) ~= domain
```

`~=` must be classified into explicit equivalence levels:

```text
SyntaxEquivalent
StructuralEquivalent
LogicalEquivalent
ProjectionEquivalent
PartiallyEquivalentWithLoss
NotEquivalent
```

Verification should support:

- source -> IR -> target -> IR round trips;
- property-based testing;
- counterexample generation;
- target-specific proof rules;
- explicit unsupported semantics.

### Acceptance criteria

A proof report can say exactly:

- what was compared;
- under which equivalence definition;
- what passed;
- what was approximated;
- what was unsupported;
- what evidence was produced.

---

## 3.9 Explainability

Every generated decision must answer:

```text
What was generated?
Why was it generated?
Where did it come from?
What meaning was preserved?
What meaning was lost?
What would be affected by changing it?
```

Example:

```text
minimum: 18
Derived from: Customer.Age
Source: Domain.fs
Constraint: age >= 18
Fidelity: ExactSemantic
Targets: PostgreSQL, TypeScript, OpenAPI
```

### Acceptance criteria

`canonflow explain Customer.Age` returns a human-readable and machine-readable explanation.

---

## 3.10 Drift and Impact Analysis

CanonFlow should compare semantics, not only text.

Examples:

```text
DB: age >= 18
OpenAPI: age > 18
TypeScript: no validation
```

CanonFlow should report:

```text
Boundary drift
Severity: High
Affected targets: OpenAPI, TypeScript
Recommended action: regenerate or acknowledge deviation
```

Impact analysis should identify:

- affected entities;
- target artifacts;
- dependent contracts;
- downstream domain packs;
- proof status changes.

### Acceptance criteria

A model change produces a deterministic semantic impact report before files are emitted.

---

## 3.11 AI-Agent Semantic Interface

The AI sidecar should expose verified semantic queries rather than entire repository dumps.

Example queries:

```text
explain Customer
constraints Customer.Age
impact Order.CustomerId
can-change Invoice.TaxRate
unsupported-projections ClaimCommand
proof-status Customer.Email
```

The sidecar should return:

- structure;
- constraints;
- relationships;
- provenance;
- fidelity;
- risk;
- safe modification guidance;
- known unknowns.

Claims such as “90% token reduction” or “eliminates hallucinations” must remain benchmark hypotheses until measured.

Defensible positioning:

> CanonFlow reduces semantic uncertainty and repository exploration by giving agents structured, verified context.

---

## 4. F# Types to Canon IR

F# ingestion should follow this pipeline:

```text
F# project
  -> compiler-service semantic model
  -> type graph
  -> classify scalar/product/sum/optional/collection/refinement
  -> extract declared constraints
  -> preserve unknown invariants as opaque
  -> normalise into Canon IR
  -> compute metrics
  -> plan target emission
```

### Core mappings

| F# concept | Canon IR concept |
|---|---|
| Primitive type | Scalar |
| Record | Product |
| Payload-free DU | Enum-like Sum |
| Payload-carrying DU | Tagged Sum |
| `option` | Optional/nullable policy |
| `list`, array, sequence | Collection |
| Single-case DU with validated constructor | Named Refinement when invariant is known |
| Tuple | Anonymous Product |
| Smart-constructor invariant not statically known | Opaque semantic node |
| Domain command/event | Behavioral declaration, not automatically a DB constraint |

Important rule:

> Canon IR describes what the F# type means, not how a particular database stores it.

---

## 5. Emit Routing Learned from Samples

The samples should remain adversarial experiments, not customers that automatically control the kernel.

## 5.1 Recommended routing summary

| Sample | Recommended physical target | Qualification |
|---|---|---|
| `trading-core` | PostgreSQL relational | Product-heavy, payload-free enums; clean exact baseline |
| `banking-core` | PostgreSQL relational | Child tables for lists; workflow remains application logic |
| `layam` / `kutcheri` | PostgreSQL relational | Good enum/range baseline |
| `sangam-credit` | PostgreSQL + DOMAINs | Best fit for named scalar refinements |
| `airline-core` | PostgreSQL relational | Composite keys and foreign keys are the core semantics |
| `hospital-core` | DuckDB UNION for structural demonstration; PostgreSQL JSONB or normalised union tables for OLTP | Separate structural fidelity from deployment suitability |
| `mudra` / pharma | Relational facts + versioned rule evaluations | Algorithmic and temporal rules stay in the rule engine |
| `GSTFlow` / EDI | Canonical document + immutable verdict/facts store; optional JSONB | Store inputs, outputs, rule versions, evidence, and verdicts |

## 5.2 Sample governance rule

A sample discovery enters the stable CanonFlow kernel only when:

- it appears in multiple domains;
- it has a clear semantic abstraction;
- target behavior is understood;
- fidelity can be specified;
- tests and counterexamples exist.

Otherwise it remains:

```text
experimental proposal
sample-specific adapter
research spike
```

---

## 6. CanonFlow, Samples, and GSTFlow Boundaries

## CanonFlow owns

- Canon IR;
- constraint and expression algebra;
- normalisation;
- equivalence and verification;
- fidelity;
- provenance;
- target capabilities;
- generic emit planning;
- generic proof reports;
- AI-agent semantic query protocol.

## CanonFlow.Samples owns

- adversarial domain experiments;
- executable architecture examples;
- conformance fixtures;
- target-routing case studies;
- documented discoveries;
- counterexamples;
- sample-specific comparisons.

Each sample should include a `DISCOVERIES.md` recording:

- unsupported rule classes;
- awkward IR representations;
- emitter limitations;
- proposed generic abstractions;
- reasons not to generalise yet.

## GSTFlow owns

- GST domain types;
- GST rule packs;
- legal/reference metadata;
- rule effective dates;
- invoice/document ingestion;
- GST explanations;
- filing-oriented projections;
- reconciliation;
- privacy-first UX;
- GST-specific fixtures and tests.

GSTFlow should depend on CanonFlow only where CanonFlow provides genuine generic leverage.

---

## 7. GSTFlow as the First Serious Customer

CanonFlow is not fully complete, but it is mature enough to support one focused customer if both evolve in a disciplined loop.

Recommended strategy:

```text
Freeze the CanonFlow philosophy and public semantic contracts.
Do not freeze bug fixes or missing capabilities.
Build GSTFlow as a real user-facing product.
Classify every discovered issue.
```

Issue classification:

```text
General semantic defect -> CanonFlow
General missing abstraction -> CanonFlow experimental proposal
GST-specific law/rule -> GSTFlow
GST-specific UX -> GSTFlow
Target-specific emitter bug -> relevant CanonFlow emitter
```

Effort guideline:

```text
80% GSTFlow product validation and user value
20% CanonFlow defects and proven general enhancements
```

Exception:

If GSTFlow exposes a fundamental flaw in Canon IR, fidelity, provenance, or verification, pause and correct the foundation before expanding product scope.

---

## 8. GSTFlow Immediate Product Scope

Recommended external product sequence:

```text
Phase 1: Private invoice checker
Phase 2: Batch and ERP validation engine
Phase 3: Filing-oriented output and reconciliation
Phase 4: Adapters, rule packs, and agent integrations
```

The first serious flow should be:

```text
invoice input
  -> canonical GST document
  -> supported rule evaluation
  -> plain-language explanation
  -> filing-oriented output
  -> proof/verdict envelope
```

A verdict envelope should include:

```text
input hash
engine version
rule-pack version
rule effective date
rules executed
facts derived
verdicts
unsupported checks
output hash
```

Do not claim total GST-law compliance. A defensible claim is:

> GSTFlow proves that its supported, versioned rules were consistently evaluated against the supplied data.

---

## 9. Prioritised Roadmap

## P0 — Foundation consolidation

- Publish Canon IR v1 draft.
- Define equivalence levels.
- Define fidelity levels.
- Add provenance to IR nodes.
- Publish target capability schema.
- Ensure emitters consume Canon IR only.

## P1 — Explainable emit planning

- Implement structural metrics.
- Add `canonflow analyze`.
- Add `canonflow route`.
- Produce recommendations, alternatives, confidence, and warnings.
- Separate structural fidelity from operational suitability.

## P2 — Verification and proof

- Implement round-trip verification.
- Add property-based and counterexample tests.
- Emit machine-readable proof reports.
- Add semantic drift detection.

## P3 — GSTFlow product grounding

- Build the private invoice checker.
- Version GST rules.
- Produce evidence-rich verdicts.
- Test with accountants, practitioners, and small businesses.
- Feed only general findings into CanonFlow.

## P4 — Agent sidecar

- Expose explain, constraint, impact, and proof queries.
- Benchmark context reduction and error reduction.
- Publish measured results rather than speculative percentages.

---

## 10. Non-Goals for the Current Stage

Do not prioritise:

- dozens of database providers;
- dozens of emitters;
- a large migration platform;
- a cloud control plane;
- enterprise dashboards;
- universal workflow modeling;
- automatic database choice without workload input;
- forcing every domain rule into the database;
- claiming complete formal proof for unsupported semantics.

---

## 11. Expected Outcome

If this roadmap is executed, CanonFlow should become:

> A semantic compiler and knowledge layer for enterprise systems that turns source structures and rules into explainable, target-aware, fidelity-graded, verifiable artifacts for humans and AI agents.

Its primary value would no longer be “F# to SQL” or “database to TypeScript.”

Its value would be:

```text
one explicit semantic model
  -> many accountable projections
  -> known fidelity
  -> traceable origins
  -> explainable decisions
  -> safe impact analysis
  -> reduced semantic uncertainty
```

GSTFlow would then serve as the first serious proof that this architecture can deliver practical user value rather than remaining only an elegant research system.
