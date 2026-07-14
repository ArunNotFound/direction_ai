# CANONFLOW_BASE.md — The Compliance Execution Constitution

> *Source → Interpretation → Type → Rule → Test → Review → Evidence → Verdict.*
>
> Implementation is never the first proof. A feature exists only when its
> contract, falsifier, custodian, release evidence, and truthful status exist.
> This document is the normative base for CanonFlow, GSTFlow, Rule Pack Studio,
> Crucible, CanonFlow Format, and every future `Flow(K)` product.

---

## 0 · PRIME LAW

```text
∀K : Flow(K) = CF ⊕ K
```

```text
CF          = CanonFlow       (domain-agnostic verification substrate)
K           = Knowledge       (GST, EDI, or another governed domain)
Flow(K)     = CF ⊕ K          (a domain product)

GSTFlow     = Flow(GST)
EDIFlow     = Flow(EDI)
```

The operational form is larger because compliance truth needs people and
evidence as well as code:

```text
TrustedFlow(K) = CF ⊕ K ⊕ H ⊕ E

H = accountable human interpretation and review
E = reproducible evidence from tests, sources, signatures, and execution
```

Therefore:

```text
Code without H = unreviewed automation
Code without E = an assertion
H without replay = an opinion that cannot be reproduced

∴ Trust = Determinism × Provenance × Review × Contact
```

If any factor is zero, production trust is zero.

CanonFlow also governs transformations between representations:

```text
introspect(emit(domain)) ≅ domain       with classified loss
decode(encode(value))    ≅ value        with classified loss
normalize(normalize(x))  = normalize(x)
evaluate(normalize(x))   = evaluate(x)
```

`≅` never means “close enough.” Every mismatch is emitted as evidence using
the fidelity law in §34. Silent semantic loss is unconstitutional.

---

## 1 · MISSION LAW

```text
GSTFlow exists to help honest businesses detect preventable GST defects
before filing, payment, transport, claiming credit, or accepting evidence.
```

GSTFlow is:

```text
offline-first · deterministic · advisory · evidence-producing
structural · arithmetic · authenticity-aware · reconciliation-capable
```

GSTFlow is never:

```text
✗ an ERP                 ✗ accounting books
✗ a filing authority    ✗ a legal opinion
✗ a promise of credit   ✗ a penalty guarantee
✗ a cloud data trap     ✗ an AI oracle
```

The product statement is binding:

> **GSTFlow is an offline deterministic GST preflight engine. It identifies
> structural, arithmetic, authenticity, statutory, and cross-document risks;
> reports missing facts and evidence; and preserves a reproducible audit trail.**

Any stronger claim requires independent evidence and a constitutional amendment.

---

## 2 · OWNERSHIP LAW

```text
Generic(x)       ⇒ x ∈ CanonFlow
GSTSpecific(x)   ⇒ x ∈ GSTFlow
Authoring(x)     ⇒ x ∈ Studio
Assurance(x)     ⇒ x ∈ Crucible
PortableProof(x) ⇒ x ∈ CFF
Distribution(x)  ⇒ x ∈ Registry
```

| Component | Owns | Must not own |
|---|---|---|
| `CanonFlow.Core` | verdict envelope, evidence, canonical digest, generic pack verification | GST sections, rates, invoice semantics |
| `GSTFlow.Core` | GST canonical facts and document types | UI, OCR, network, database |
| `GSTFlow.Rules` | compiled GST laws and bounded rule evaluation | AI, portal submission, visual concerns |
| `CanonFlow.Studio` | authoring, examples, review workflow, signing request | final legal authority, unrestricted code generation |
| `CanonFlow.Crucible` | corpus execution, properties, mutation, agreement, proof manifest | deciding law by itself |
| `CanonFlow.CFF` | deterministic evidence bundle and interchange | mutable database semantics |
| `GSTFlow.Intake` | raw adapters and normalization | statutory verdicts |
| `GSTFlow.Reconcile` | typed cross-document matching | changing source evidence |
| `GSTFlow.UI` | Avalonia presentation and user workflow | duplicate rule logic |
| Registry | discovery, trust metadata, supersession, revocation | executing packs or receiving taxpayer data |

Deletion proof:

```text
delete(GSTFlow)  ⇒ CanonFlow and EDIFlow still build
delete(EDIFlow)  ⇒ CanonFlow and GSTFlow still build
delete(Studio)   ⇒ released packs still execute and replay
delete(Registry) ⇒ files still verify and transfer offline
delete(AI)       ⇒ every verdict and release test still works
```

Promotion into `CanonFlow.Core` requires all three:

```text
DomainNeutral(x)
∧ ExercisedInRealFlow(x)
∧ SecondFlowUsesUnchanged(x)
```

GSTFlow may prove a need; EDIFlow or another independent `Flow(K)` must prove
generality. Until then, the capability remains domain-owned. Premature reuse is
another form of coupling.

---

## 3 · TRUST HIERARCHY

The expected result for a statutory test must come from a traceable oracle:

```text
Authority
  > reviewed interpretation record
  > independently checked worked example
  > hand-calculated expected amount
  > approved invariant or metamorphic relation
  > prior implementation output
```

The current engine is the weakest oracle. Never execute the implementation and
save its answer as the expected answer without independent derivation.

```text
AI suggestion ≠ authority
Test count     ≠ legal correctness
Type safety    ≠ legal correctness
Signature      ≠ legal correctness
```

A signature proves who approved specific bytes. A test proves that an
implementation violated or satisfied a stated proposition for tested inputs.
Neither proves that an interpretation of law is universally correct.

Facts and transformations also carry a provenance grade:

```text
Observed = read directly from preserved source evidence
Derived  = reproducibly calculated from identified inputs and function
Declared = asserted by an accountable author/reviewer
Guessed  = proposed by OCR/AI/heuristic and awaiting confirmation
Opaque   = origin or derivation cannot be established
```

```text
Guessed ∪ Opaque cannot independently justify Pass or Fail.
Derived must identify its inputs and derivation version.
Declared must identify its accountable declarant.
```

---

## 4 · ONE-ENGINE LAW

```text
verify : CanonicalFacts → EngineVersion → RuleContext → VerdictEnvelope
```

The authoritative verdict path is pure managed F#/.NET using
`System.Decimal`. Every shell calls this path. No shell reimplements it.

```text
∀ host ∈ SupportedHosts :
    canonical(host.verify(x, e, p)) = canonical(reference.verify(x, e, p))
```

Primary hosts:

```text
CLI             = .NET NativeAOT reference executable
Desktop         = Avalonia + managed F#/.NET
Mobile Pro      = Avalonia + managed F#/.NET, after platform proof
Web inspector   = Fable/JS only within proven compatibility
```

Forbidden:

```text
✗ Flutter/Dart verdict logic
✗ Fable-Dart monetary logic
✗ native ABI verdict bridge
✗ JavaScript Number as statutory money
✗ LLM-selected Pass/Fail
✗ UI-local rule calculation
```

Fable is a target, not an assumption. If the Fable host fails one canonical
decimal or verdict agreement case, it becomes an editor/inspector only until
agreement is restored.

Core results contain structured meaning, never localized prose:

```text
RuleResult = RuleId + Outcome + MessageKey + TypedParameters
             + Evidence + Provenance
```

Formatting, currency display, interpolation, and translation occur at the
presentation boundary. This prevents locale or runtime formatting differences
from corrupting canonical agreement.

---

## 5 · EXACT-MONEY LAW

```text
Money = Decimal(amount, currency, scale, provenance)

∀ monetary operation in VerdictPath : numericType = System.Decimal
```

No conversion through binary floating point is permitted.

```text
JSON wire value     = canonical decimal string
Avro candidate      = logicalType decimal with explicit precision and scale
DuckDB candidate    = DECIMAL(p,s) with proven round-trip agreement
UI display          = formatting only; never the oracle value
```

Every monetary policy must state:

```text
scope · scale · midpoint mode · aggregation order · tolerance · authority
```

Boundary tests are mandatory immediately below, at, and above each approved
threshold. A tolerance is a reviewed policy, not a convenient constant.

```text
Forbidden = float ∪ double ∪ JS Number ∪ Dart double
            on any statutory monetary path
```

---

## 6 · OUTCOME AND UNCERTAINTY LAW

The engine must not compress uncertainty into success.

```fsharp
type RuleOutcome =
    | Pass
    | Warning
    | Unknown of MissingFact list
    | NeedsEvidence of EvidenceRequirement list
    | RequiresProfessionalReview of LegalIssue
    | Fail
```

Precedence is an explicit function, never the declaration order of a
discriminated union:

```text
severity : RuleOutcome → Severity
aggregate = reduce(severity, explicit policy)
```

Required laws:

```text
Fail present                 ⇒ Overall = Fail
Missing decision fact       ⇒ Overall ≠ Pass
Unsupported rule family     ⇒ explicit NotSupported evidence
Conflicting interpretations ⇒ RequiresProfessionalReview
```

Logical composition of unknown facts follows an explicit three-valued policy,
such as strong Kleene logic, rather than accidental Boolean defaults:

```text
Not(Unknown)             = Unknown
Violated AND Unknown     = Violated
Satisfied AND Unknown    = Unknown
Satisfied OR Unknown     = Satisfied
Violated OR Unknown      = Unknown
```

Statutory result aggregation is separate from predicate evaluation and remains
governed by the explicit severity function.

Individual UI may compress outcomes into green/amber/red, but the canonical
envelope retains the full state and the CA view exposes it.

---

## 7 · CANONICAL INTAKE LAW

```text
External → Raw → Parsed → Normalized → CanonicalFacts → Verify
```

The engine receives facts, not guessed documents. Intake preserves:

```text
raw value · parsed value · normalized value · confidence class · source pointer
```

Supported adapter families, activated by evidence gates:

```text
GST e-invoice JSON          CSV / Excel ledgers
ERP and accounting exports PDFs and scanned images
IRP signed QR payloads      GSTR export files
e-way-bill exports          manual entry
CFF evidence bundles        rule-pack and corpus files
```

Canonical document classes include:

```text
TaxInvoice · BillOfSupply · CreditNote · DebitNote
AdvanceReceipt · ImportDocument · ExportDocument · ISDDocument
Delivery/TransportEvidence · UnknownDocument
```

Rules:

```text
OCR/AI result = candidate fact
guessed critical field ⇒ confirmation required
normalization never overwrites raw evidence
adapter failure ⇒ diagnostic Result, never process crash
```

Primitive identity must survive refinement. Two values with the same runtime
representation but different proofs remain different types:

```text
Refined<decimal, TaxableValue> ≠ Refined<decimal, TaxAmount>
Refined<string, GSTIN>         ≠ Refined<string, InvoiceNumber>
Digest<Subject>                ≠ Digest<RulePack>
```

Phantom tags or erased units may encode the identity without runtime cost.

Every intake family declares three separate layers:

```text
L1 Contract validation     = structure, types, code lists, arithmetic
L2 Transport/integration   = portal/API/file movement, acknowledgements
L3 Bilateral/legal mapping = business meaning, master-data equivalence,
                              professional judgment
```

Passing L1 does not prove L2 delivery or L3 business/legal correctness.

---

## 8 · GST CAPABILITY LAW

The full capability map is a country; release gates select the road.

### 8.1 · Document integrity

```text
document type · invoice number · date · amendments
seller/buyer identity · GSTIN structure/checksum · state codes
line structure · HSN/SAC form · quantity/UOM · totals · duplicates
```

### 8.2 · Arithmetic and tax structure

```text
taxable value · discounts · assessable value · rate application
IGST versus CGST+SGST/UTGST exclusivity · cess · totals
line-to-document reconciliation · reviewed rounding policy
credit/debit adjustments · inclusive/exclusive utility calculation
```

### 8.3 · Statutory transaction rules

```text
Place of Supply for scoped goods/services branches
interstate/intrastate determination
Reverse Charge screening
time and value of supply checks
rate/exemption/notification applicability
composition-document restrictions
exports, SEZ, deemed exports, zero-rated evidence
advances, continuous supply, job work where facts support them
TDS/TCS and special mechanisms only as separately reviewed families
```

### 8.4 · Input tax credit preflight

```text
document/evidence presence · Section 16 condition screening
blocked-credit classification · time-bound screening
duplicate/already-claimed risk · reversal indicators
purchase-register versus statement reconciliation
```

GSTFlow reports screening risk and missing evidence. It does not guarantee
credit eligibility because receipt, use, payment, filing, litigation, and other
facts may sit outside the document.

### 8.5 · Authenticity and movement

```text
IRN and signed-QR parsing · signature verification · field agreement
IRN duplicate detection · invoice/QR mismatch
e-way-bill applicability warning · invoice/e-way-bill consistency
vehicle/transporter/document reference checks where imported
```

Verification is not generation or submission.

### 8.6 · Returns and reconciliation

```text
sales ledger ↔ GSTR-1/1A
purchase register ↔ GSTR-2B
GSTR-1 ↔ GSTR-3B
books ↔ e-invoice ↔ e-way-bill
original ↔ amendment ↔ credit/debit note
period totals · exceptions · explainable match confidence
```

### 8.7 · Advanced governed families

```text
refund preflight · export/LUT evidence · ISD
multi-registration and branch transfer · special valuation
retrospective amendments · conflicting authority handling
audit workpapers and historical replay
```

Each family is `NotSupported` until its own sources, interpretation, corpus,
custodian, implementation, and contact gate are complete.

---

## 9 · CONTEXT LAW

An invoice is not the whole transaction.

```text
Decision = DocumentFacts ⊕ TaxpayerContext ⊕ TransactionContext
           ⊕ EvidenceContext ⊕ TemporalContext
```

Potential required context includes:

```text
registration type · turnover band · filing frequency · supplier status
recipient status · SEZ/LUT/bond · business purpose · goods/services class
receipt/payment dates · related-party facts · transport facts
applicable notification · evidence of receipt/use · return-period records
```

If a required fact is absent:

```text
¬fact ⇒ Unknown | NeedsEvidence | RequiresProfessionalReview
```

Never infer legal facts from a convenient proxy such as a buyer GSTIN when
delivery, use, contract, or place-of-supply facts are required.

---

## 10 · TEMPORAL AND REPLAY LAW

```text
verify(d, engineDigest, packDigest, effectiveContext) = constant
∀ wall-clock time
```

Every verdict records:

```text
input digest · normalization version · engine version/digest
rule-pack ID/version/digest · effective date · evaluation timestamp
rules evaluated · evidence references · canonical outcome digest
```

Published artifacts are immutable. Correction creates a successor with:

```text
supersedes · reason · affected period · migration/re-evaluation guidance
```

The system must distinguish:

```text
law effective date
notification/publication date
pack publication date
evaluation date
retrospective applicability
```

Historical packs remain available for replay. The newest pack never silently
rewrites an old verdict.

---

## 11 · RULE CAPABILITY AND UPDATE LAW

The original binary-versus-pack split is retained, but Studio requires a
precise three-class model.

### 11.1 · Class P — Parameter pack

```text
P = tables, identifiers, dates, rates, mappings, templates, public keys
```

Examples:

```text
HSN/SAC rate map · state codes · UOM · notification effective dates
IRP public keys · language templates · compatibility metadata
```

Class P contains no predicates or control flow. It may be imported after
signature, schema, compatibility, and effective-date verification.

### 11.2 · Class D — Bounded decision pack

```text
D = finite acyclic decision graph over allowlisted facts and operators
```

Class D is data, not native code, but it can influence a verdict through a
fixed total interpreter. Therefore it receives a higher trust gate:

```text
finite graph · no loops · no recursion · no reflection · no I/O
no user-defined functions · bounded nodes/depth/time · decimal-only money
declared fact dependencies · exhaustive branches · explicit uncertainty
dual statutory review · mutation tests · Gold Corpus · revocable signature
```

Class D is not enabled for general distribution until the interpreter,
resource bounds, governance, and adversarial-pack suite are independently
reviewed.

### 11.3 · Class E — Engine change

```text
E = new fact type, operator, outcome semantics, canonicalization, algebra,
    cryptographic policy, or document class
```

Class E requires source change, compilation, complete Crucible execution, and
engine release. Studio may prepare a proposal, but cannot publish it as a pack.

```text
rate/date/table change          ⇒ P
bounded reviewed decision tree ⇒ D
new semantic capability        ⇒ E
arbitrary source/binary        ⇒ REJECT
```

---

## 12 · RULE PACK STUDIO LAW

Studio is a statutory authoring and review instrument, not a code vending
machine.

```text
Source material → Interpretation record → Visual graph/table
→ Candidate Rule IR → Crucible → Human review → Signature → Pack
```

Required features:

```text
visual decision graph          typed fact/operator palette
parameter-table editor         effective-date timeline
legal-source attachments       interpretation record
worked-example editor          boundary partition assistant
missing-branch diagnostics     unreachable/cycle detection
Gold Corpus runner             property and mutation runner
pack diff                      impact report
author/reviewer workflow       signing and export
revocation/supersession draft  proof-manifest viewer
```

Studio normalizes candidate graphs into a canonical form before review:

```text
normalize : CandidateRuleIR → NormalizedRuleIR

normalize(normalize(r)) = normalize(r)
evaluate(normalize(r))  = evaluate(r)
```

Normalization performs duplicate elimination, safe interval intersection,
contradiction detection, stable ordering, unreachable-node reporting, and
exhaustiveness analysis. A contradiction becomes a blocking diagnostic; it is
never optimized into an unexplained result.

Primary implementation:

```text
CanonFlow.Studio = Avalonia + F#/.NET desktop utility
```

Desktop is primary because large corpora, filesystem access, certificate
stores, PKCS#11/DSC hardware, and offline review are first-class requirements.

Optional browser Studio Lite may draft graphs, run structural checks, inspect
packs, and export unsigned candidates. It cannot become the production signer
or a second semantic engine without all agreement and security gates.

Forbidden:

```text
✗ arbitrary F# generation for distributed execution
✗ arbitrary DLL/assembly packs
✗ author self-approval
✗ AI auto-publication
✗ hidden legal assumptions
```

---

## 13 · CRUCIBLE LAW

Crucible is an executable assurance product, not scattered workflow YAML.

```text
crucible restore
crucible validate-source
crucible validate-pack
crucible run-examples
crucible run-boundaries
crucible run-properties
crucible run-mutations
crucible compare-hosts
crucible test-aot
crucible test-security
crucible produce-proof
```

The proof manifest records:

```text
source digests · interpretation digests · engine digest · pack digest
corpus digest · toolchain versions · seeds · test results
critical mutations · target agreement · AOT/trim warnings
review identities · artifact digests · known limitations
```

A green badge without these inputs is insufficient evidence.

---

## 14 · TESTING LAW

### 14.1 · Test layers

```text
T0 repository integrity
T1 domain constructors and invalid-state prevention
T2 exact arithmetic and boundary matrices
T3 reviewed statutory examples
T4 FsCheck properties with reproducible seeds and shrinking
T5 metamorphic relations
T6 mutation testing
T7 Gold Corpus
T8 cross-host canonical agreement
T9 pack/parser/security fuzzing
T10 NativeAOT and FFI survival
T11 Avalonia headless and critical end-to-end flows
T12 historical replay and migration
```

### 14.2 · Required general properties

```text
same pinned input ⇒ same canonical verdict
serialization round-trip preserves meaning
missing required fact never becomes Pass
Fail cannot be hidden by weaker outcomes
irrelevant-field change does not alter a rule
expired/incompatible pack is rejected
every finding names a rule and evidence path
every crashable input becomes a typed diagnostic
arbitrary garbage at every public parser never escapes as an unhandled error
bounded reference corpus completes within its declared resource budget
```

### 14.3 · Mutation operators

```text
> ↔ >= · All ↔ Any · remove exception · alter decimal constant
swap IGST/CGST · bypass effective date · reorder severity
accept invalid signature · omit digest · truncate evidence
```

Coverage percentage is telemetry. Release approval depends on killing every
identified critical mutation and satisfying the governed corpus.

Snapshots and golden outputs are review artifacts, not self-writing oracles:

```text
missing snapshot ⇒ test failure + explicit review action
changed snapshot ⇒ reviewed semantic diff
test execution   ⇒ never auto-approves a new expectation
```

Skipping unsupported generated cases is permitted only when the fidelity report
records why they were skipped and the release capability manifest excludes the
claim. `if unsupported then true` without evidence is a vacuous test.

---

## 15 · AI-ASSISTED ASSURANCE LAW

AI is an adversarial assistant outside the deterministic release path.

### 15.1 · Permitted work

```text
extract candidate propositions from cited sources
propose decision tables and missing branches
generate boundary partitions and synthetic facts
propose FsCheck properties and mutations
compare source/pack versions
cluster failures and minimize explanations
detect documentation/code/status disagreement
draft review notes and lay explanations
```

### 15.2 · Forbidden authority

```text
AI expected verdict          ✗ unless independently derived and approved
AI changing Gold expectation ✗
AI merging statutory PR      ✗
AI signing/publishing pack   ✗
AI receiving taxpayer PII    ✗ unless locally authorized and controlled
AI in production verdict     ✗
```

### 15.3 · Candidate-to-proof pipeline

```text
AI proposal
→ CandidateCase status
→ schema/static validation
→ statutory author review
→ independent reviewer derivation
→ ApprovedCase status
→ committed deterministic artifact
→ Crucible execution without an LLM
```

Agent-generated changes are PR-only, time/diff bounded, and require witnessed
red-run evidence before implementation. Model name, prompt/template digest,
source digest, and human disposition may be retained for provenance, but the
release must not depend on reproducing a model response.

AI agents receive an emitted, machine-readable contract rather than guessing
from source code or prose:

```text
AgentContext = allowed facts + refined constraints + lineage grades
               + fidelity gaps + safe operations + forbidden operations
               + schema/pack/engine digests
```

The context is generated from the same canonical model as runtime validation.
If an emitter cannot represent a constraint exactly, the limitation is included
as data. The agent is never shown an approximate contract as exact truth.

---

## 16 · GOLD CORPUS LAW

```text
Gold = PublicSynthetic ⊕ RestrictedSanitized ⊕ HistoricalRegressions
```

Every case contains:

```text
case ID · facts · expected outcome/value · applicable period
authority references · interpretation record · boundary rationale
author · legal reviewer · technical reviewer · approval status · digest
```

Every confirmed defect adds:

```text
minimal reproducer ⊕ surrounding boundary family ⊕ missed-risk explanation
```

Corpus privacy and licensing are explicit. Real invoices are never committed
without permission and sanitization. Synthetic data must remain legally
meaningful rather than random noise.

`Contact ≥ 1` is separate: a synthetic corpus cannot replace an external user
or professional validating the product on a real workflow.

---

## 17 · HOST AND DEPLOYMENT LAW

### 17.1 · Avalonia

```text
GSTFlow Desktop = Avalonia UI + F#/.NET
GSTFlow Mobile Pro = Avalonia only after platform-specific proof
```

Testing order:

```text
view-model/service tests → Avalonia headless tests
→ visual regression where meaningful → critical real-platform automation
```

### 17.2 · NativeAOT

```text
build success ≠ NativeAOT success
```

CI publishes the real artifact, treats unresolved relevant AOT/trim warnings as
release blockers, launches the binary, and executes the canonical corpus.

### 17.3 · Fable

```text
Fable host = supported only if agreement suite is green
```

The web gateway may provide stateless intake, draft inspection, and preflight
only within proven numeric/serialization compatibility. It is never assumed to
inherit `System.Decimal` semantics automatically.

### 17.4 · Platform activation

```text
Desktop + CLI first
Web only after agreement
Mobile only after demand, lifecycle, camera, signing, and AOT proof
```

No platform exists merely because a project compiles.

---

## 18 · CFF LAW

CanonFlow Format is a portable evidence container, not a magical database.

```text
CFF = deterministic archive(manifest, sources, normalized facts,
                            verdicts, evidence, attachments, proof)
```

Normative logical layout:

```text
manifest.json
sources/
facts/
verdicts/
evidence/
attachments/
proof-manifest.json
signatures/
```

Required properties:

```text
path safety · size/count limits · per-entry digests · canonical manifest
schema versions · engine/pack digests · privacy classification
optional source inclusion · deterministic ordering · safe extraction
```

Storage evolution:

```text
v1 canonical JSON        = first interoperable proof
Avro                     = enabled after decimal/union round-trip proof
Parquet/Arrow            = enabled for analytical projection after proof
DuckDB                   = optional query/index sidecar, never verdict oracle
```

Digest is not signature. Encryption is not authenticity. Signature is not
legal correctness. UI and documentation must use the exact term implemented.

---

## 19 · RULE-PACK AND SIGNATURE LAW

```text
RulePack = manifest ⊕ content ⊕ proof ⊕ approvals ⊕ signatures
```

Verification order:

```text
safe container parse
→ canonical digest
→ signature and certificate/trust policy
→ revocation/supersession state
→ schema and engine compatibility
→ jurisdiction/effective period
→ semantic validation
→ activation
```

Trust roles are separate:

```text
author approval        = professional identity/DSC workflow where validated
review approval        = independent signer
release signature      = Foundation release key
transport integrity    = SHA-256 digests
```

Browser Web Crypto is not assumed to support government DSC USB-token flows.
Production signing belongs in the desktop Studio through a validated OS
certificate-store or PKCS#11/provider integration.

Offline revocation uses signed trust snapshots and explicit expiry/staleness
warnings. The engine never silently trusts an unknown signer.

---

## 20 · AUTHENTICITY LAW

```text
Authentic(payload) ≠ CorrectTaxTreatment(payload)
```

QR/e-invoice verification requires:

```text
real payload parser · algorithm allowlist · trusted key set
signature verification · certificate/key validity policy
QR-to-visible-invoice field agreement · malformed-input fuzzing
known-answer vectors · key rotation and staleness handling
```

The result distinguishes:

```text
SignatureValid · SignatureInvalid · KeyUnknown · PayloadMalformed
FieldsAgree · FieldsMismatch · VerificationNotSupported
```

No hardcoded success, demo key, or base64 decode may be presented as signature
verification.

---

## 21 · RECONCILIATION LAW

```text
reconcile : SourceSet → MatchGraph → Exceptions → Evidence
```

Reconciliation is not one Boolean. It produces:

```text
exact match · deterministic normalized match · candidate match
missing left · missing right · duplicate · conflicting amounts
period mismatch · amendment chain · unresolved
```

Rules:

```text
source records remain immutable
normalization version is recorded
fuzzy/AI match never becomes exact without confirmation
every aggregate drills down to documents
DuckDB accelerates selection/aggregation, not statutory judgment
```

Initial high-value order:

```text
purchase register ↔ GSTR-2B
sales ledger ↔ GSTR-1
GSTR-1 ↔ GSTR-3B
invoice ↔ IRN/QR ↔ e-way bill
```

---

## 22 · AI PRODUCT LAW

AI features are optional peripherals:

```text
PDF/image extraction · source summarization · candidate classification
natural-language query drafting · explanation drafting · voice NLU
```

Required quarantine:

```text
AI output labeled · confidence class not false percentage
critical facts confirmed · prompt injection treated as hostile input
no tool authority · no signing key access · no verdict mutation
deterministic template remains explanation of record
```

Local AI may run out-of-process. Its crash, timeout, thermal limit, or model
absence must degrade to manual/deterministic workflows.

```text
delete(AI) ⇒ validate, explain-by-template, replay, export all remain true
```

---

## 23 · FFI AND CRASH-CONTAINMENT LAW

F# reduces some error classes; it is not immunity from process crashes.

```text
Managed verdict kernel = no unsafe/native dependency
Native peripheral      = explicit adapter + bounded data contract
```

Native candidates include OCR, camera codecs, DuckDB, compression, and local
AI runtimes. Every boundary requires:

```text
ABI/version check · ownership/lifetime specification · cancellation policy
size limits · malformed-response tests · repeated open/close soak
missing-library behavior · crash recovery · platform matrix
```

Where feasible, crash-prone native work runs in a helper process:

```text
native crash ⇒ operation failure + recoverable diagnostic
             ≠ corrupted verdict or lost workspace
```

Money crossing a peripheral boundary uses canonical decimal strings or scaled
integers with explicit scale; never `double`.

---

## 24 · OFFLINE AND PRIVACY LAW

```text
VerdictPath ⇒ ¬Network
```

Product modes:

```text
Web inspector     = stateless by default
Installed utility = local workspace, explicit retention and wipe
Backup/transfer   = user-carried file
```

Privacy features:

```text
offline badge · network-call evidence · storage disclosure
one-tap wipe · source-inclusion choice · privacy receipt
no accounts · no telemetry in verdict path · no silent crash upload
```

Online operations, if ever added, are explicit adapters outside verification
and disabled by default. Offline claims must be demonstrated by tests and
observable behavior, not copy alone.

---

## 25 · PRODUCT AND UX LAW

The individual utility remains:

```text
give document → confirm facts → receive verdict → understand → act/share
```

Required surfaces:

```text
single-document check       extracted-field confirmation
plain-language finding      monetary impact where defensible
why/evidence drill-down     missing-fact request
shareable verdict card/PDF  privacy receipt
pack/version freshness      unsupported-capability matrix
```

Installed professional modes may add:

```text
batch/ZIP/folder intake · pending/verified workspace
filters and exceptions · client workpapers · print/export
pack pinning · reconciliation · backup/restore · duplicate detection
```

Modes arrange features; they are not server-side roles or paid authority.

UI copy never says “compliant”, “approved”, “guaranteed”, or “ready to file”
when the engine has performed only preflight checks.

---

## 26 · ACCESSIBILITY AND LANGUAGE LAW

```text
Meaning(rule) is language-independent
Template(rule, language) is deterministic presentation
```

Every shipped language requires native-speaker review and template completeness.

Features:

```text
screen readers · keyboard navigation · high contrast · scalable text
large touch targets · color-independent outcomes · reduced motion
deterministic voice output · digit-by-digit confirmation for voice input
```

Voice output may use platform TTS. Voice input is always a candidate fact path
through confirmation. A misheard GSTIN digit never reaches validation as a
confirmed fact.

Language rollout follows real user demand and reviewer availability, not the
number of machine translations that can be generated.

---

## 27 · STORAGE, BACKUP, AND PORTABILITY LAW

```text
Local-only without export = user lock-in
```

Installed workspaces store:

```text
source reference · input digest · normalized facts · verdict envelope
pack/engine versions · review state · timestamps · user annotations
```

Backup is a versioned user-carried archive:

```text
manifest · per-file digests · verdicts · optional sources · schema version
```

Restore performs safe parse, digest verification, explicit migration, and
deduplication. It never silently changes historical verdicts.

Optional encryption uses reviewed standard cryptography and clear recovery
warnings. CanonFlow does not invent a cryptographic format or promise recovery
from a forgotten passphrase.

---

## 28 · EXTERNAL-SYSTEM WALL

Offline preflight may prepare and validate payloads, but government actions are
separate state-changing integrations.

```text
validate IRN payload      ✓
verify signed QR          ✓
prepare filing/export     ✓ after schema proof
submit to IRP/GSTN        outside core; separately authorized adapter
pay tax                   never implicit
declare filing success    only from verified acknowledgement
```

An online connector, if built, must be a separate product boundary with
credentials, consent, audit, retry/idempotency, acknowledgement verification,
and applicable authorization. Its absence never blocks offline GSTFlow.

---

## 29 · CUSTODIAN LAW

Custodianship is a system, not a heroic founder.

| Role | Binding responsibility |
|---|---|
| Technical Custodian | engine contracts, architecture, target agreement |
| Statutory Custodian | interpretation records, scope, effective dates |
| Corpus Custodian | case quality, privacy, licensing, regression integrity |
| Security Custodian | threat model, signing, disclosure, revocation |
| Release Custodian | CI evidence, artifacts, provenance, publication |
| Community Custodian | contribution path, translations, reviewer growth |

Minimum production approval:

```text
author ≠ statutory reviewer
statutory approval ∧ technical approval ∧ release approval
```

Required governance files:

```text
GOVERNANCE.md · SECURITY.md · CONTRIBUTING.md · CODE_OF_CONDUCT.md
RULE_PACK_POLICY.md · LEGAL_REVIEW_POLICY.md · RELEASE_POLICY.md
SUPPORTED_CAPABILITIES.md · THREAT_MODEL.md
```

Disagreement is preserved as an issue or competing reviewed interpretation;
it is never resolved by silent majority or AI confidence.

Foundation code is stewarded as auditable public infrastructure under
Apache-2.0. Contributions use a transparent sign-off policy; architectural
authority remains human and documented through ADRs. AI agents receive no
main-branch or release-signing credentials.

---

## 30 · SUPPLY-CHAIN AND RELEASE LAW

Every release starts from a clean clone and produces:

```text
source commit · locked dependencies · compiler/toolchain versions
SBOM · dependency/license report · deterministic build metadata
artifact digests · signatures/attestations · test/proof manifest
changed rules · known limitations · rollback/revocation instructions
```

CI gates:

```text
restore/build/test                  mandatory
format/static checks               mandatory
properties/mutations/corpus        mandatory by activated capability
NativeAOT publish + execution      mandatory for AOT artifact
Avalonia headless                  mandatory for UI release
Fable agreement                    mandatory if web verdict is claimed
malicious-pack/parser suite        mandatory for pack/CFF release
documentation capability diff     mandatory
stranger workflow exercise        mandatory before first public production claim
```

Forbidden:

```text
✗ || true on a required gate
✗ skipped failing target presented as supported
✗ mutable unpinned release input
✗ unsigned artifact presented as signed
✗ release from a dirty or unreproducible workspace
```

---

## 31 · TRUTHFUL STATUS LAW

Every capability has exactly one public status:

| Status | Meaning |
|---|---|
| `PROVEN` | implementation, falsifier, agreement, clean CI, docs, custodian evidence |
| `EXPERIMENTAL` | works in bounded conditions; limitations displayed |
| `STUBBED` | interface exists; production behavior unavailable |
| `DESIGNED` | specification/ADR exists; no implementation claim |
| `DORMANT` | intentionally deferred with a wake condition |
| `REJECTED` | violates a constitutional boundary |

```text
Fixed = Implementation ∧ Falsification ∧ Agreement ∧ CleanCI ∧ TruthfulDocs
```

README, website, UI, release notes, and supported-capability manifest must agree.
CI should fail when a claimed feature lacks its required evidence artifact.

---

## 32 · DEPENDENCY LAW

```text
∀ lib ∈ VerdictPath :
    deterministic(lib) ∧ offline(lib) ∧ decimalSafe(lib)
    ∧ AOTCompatible(lib) ∧ maintained(lib)
```

For a Fable-supported path, add:

```text
FableCompatible(lib) ∧ AgreementProven(lib)
```

Preferred foundations:

```text
F#/.NET 10                  domain and execution
System.Decimal              statutory money
FsCheck                     property testing
existing test framework     examples and integration; avoid aesthetic churn
Avalonia                    installed UI
NativeAOT                   release artifact after proof
Fable                       conditional browser target
DuckDB                      optional analytical accelerator
Avro/Parquet/Arrow          gated interchange/projection formats
```

A dependency must remove more complexity than it adds. Reflection-heavy
serialization, dynamic code generation, and opaque native dependencies require
specific AOT and crash evidence. Explicit wire DTOs/codecs are preferred at
trusted boundaries.

Build-time, diffable generated artifacts are preferred over type providers,
runtime code generation, or hidden compiler magic. Generation output is checked
into review or reproduced in CI with a semantic diff and fidelity report.

---

## 33 · REPOSITORY SHAPE

```text
/src
  CanonFlow.Core
  CanonFlow.Packs
  CanonFlow.CFF
  CanonFlow.Crucible
  CanonFlow.Studio
  GSTFlow.Core
  GSTFlow.Intake
  GSTFlow.Rules
  GSTFlow.Reconcile
  GSTFlow.Cli
  GSTFlow.UI

/tests
  Unit
  Boundaries
  Properties
  Mutations
  GoldCorpus
  Agreement
  Security
  AotSmoke
  AvaloniaHeadless

/governance
  interpretations
  approvals
  revocations
  trust-snapshots

/corpus
  public-synthetic
  restricted-manifest
  regressions

/schemas
  canonical-facts
  verdict-envelope
  cff
  rule-pack
  proof-manifest
```

Physical repositories may remain separate. The ownership and dependency
direction above remain binding.

---

## 34 · FIDELITY, DRIFT, PROFILE, AND CONFORMANCE LAW

### 34.1 · Classified fidelity

Every transformation reports both value and meaning preservation:

```fsharp
type RepresentationFidelity =
    | Lossless
    | Widened of reason: string
    | Narrowed of reason: string
    | Unrepresentable of reason: string

type SemanticFidelity =
    | Exact
    | Approximate of reason: string
    | Unsupported of reason: string
    | Unknown
```

Applications include:

```text
external schema → CanonicalFacts
CanonicalFacts  → JSON/Avro/Parquet/DuckDB
Rule IR         → F#/.NET/Fable host
source rule     → UI explanation/OpenAPI/agent context
```

No emitter may silently discard parameters, effective dates, evidence,
precision, scale, uncertainty, or legal references.

### 34.2 · Semantic drift

```fsharp
type DriftStatus =
    | Aligned
    | StrictTarget
    | LooseTarget
    | Disjoint
    | ComplexOrUnknown
```

```text
Aligned          = same admitted/rejected meaning
StrictTarget     = target rejects values the source admits
LooseTarget      = target admits values the source rejects
Disjoint         = source and target are mutually incompatible
ComplexOrUnknown = implication cannot be proved by the available model
```

Drift is computed across:

```text
law source ↔ interpretation ↔ Rule IR ↔ runtime
runtime ↔ Fable host ↔ NativeAOT host
verdict schema ↔ CFF/analytics projection
capability manifest ↔ README ↔ website ↔ UI
```

High-risk loose, disjoint, or unknown drift blocks release. Approximation is a
first-class result with a reason and remediation—not a warning hidden in logs.

### 34.3 · Profile bundle

Every governed rule family is distributed and reviewed as a profile:

```text
Profile = source extracts
        ⊕ interpretation record
        ⊕ canonical rule/parameter content
        ⊕ passing fixtures
        ⊕ failing fixtures
        ⊕ boundary/property/mutation set
        ⊕ proof manifest
        ⊕ approvals
```

This absorbs EDIFlow’s useful `schema/profile/proof/fixtures` separation and
generalizes it to GST. A profile states exactly which contract layer it covers
and which transport, bilateral, factual, or professional questions remain out
of scope.

### 34.4 · Conformance kit

Extension points ship with executable conformance suites:

```text
Intake adapter      ⇒ raw preservation + normalization + round-trip laws
Storage projection ⇒ decimal/DU/evidence fidelity laws
Verdict host        ⇒ canonical agreement + crash boundary
Rule-pack authoring ⇒ schema + bounds + proof + review laws
Registry mirror     ⇒ digest/signature/revocation consistency
```

A new driver or host is accepted by passing the suite, not by inheriting an
interface or copying a sample. Conformance fixtures are versioned and reusable
by third-party contributors without access to private data.

### 34.5 · Soundness and optimizer laws

```text
evaluate(simplify(x)) = evaluate(x)
simplify(simplify(x)) = simplify(x)
roundTrip(x)          ≅ x with classified fidelity
target admits unsafe value ⇒ LooseTarget
target rejects valid value ⇒ StrictTarget
```

Optimizer, drift, and fidelity functions receive hostile deeply nested
property tests and mutation tests. Structural equality is never claimed to be
semantic equality without canonicalization or a bounded equivalence proof.

---

## 35 · COMPLETE FEATURE LEDGER

This ledger prevents older proposals from becoming invisible. Inclusion here
means “governed by this constitution,” not “implemented today.” Current status
is always determined by §31 and the release capability manifest.

### 35.1 · Foundational engine

```text
F# domain model · explicit outcome precedence · System.Decimal money
GSTIN/document primitives · canonical facts · evidence paths
verdict envelope · deterministic templates · replay · provenance
effective dates · unsupported/unknown states · exact canonical digest
```

### 35.2 · Individual utility

```text
paste/import document · camera/PDF intake · confirmation screen
single-screen verdict · plain-language correction · defensible ₹ impact
why/legal-reference view · offline/privacy receipt · share image/PDF
tap-to-call supplier · print · HSN/rate lookup · reverse-GST calculator
```

The reverse-GST calculator is a fenced utility, never part of the verdict
unless an activated reviewed rule explicitly uses its facts.

### 35.3 · Professional utility

```text
batch/folder/ZIP intake · pending/verified/needs-fix workspace
exception filters · bulk workpapers · client export · pack pinning
duplicate/double-billing detection · annotations · reconciliation
local DuckDB analytical projection · deterministic query console
CLI/TUI presentation · backup/restore · cross-device file handoff
```

Natural-language-to-query assistance may draft allowlisted read-only queries;
it is labeled AI/demonstration until a real local model, grammar enforcement,
result validation, and injection tests exist. Query results never change the
canonical verdict.

### 35.4 · Sovereign offline features

```text
IRP signed-QR verification · signed rule packs · signed trust snapshots
staleness disclosure · HSN/SAC directory · user-carried CFF bundles
offline key rotation imports · optional encrypted backup
```

### 35.5 · Studio and professional knowledge

```text
visual rule graph · parameter tables · effective-date timeline
source/interpretation records · case authoring · AI boundary assistant
Crucible runner · pack diff/impact · approval workflow · DSC signing
supersession/revocation · proof viewer · unsigned browser drafting
```

### 35.6 · Assurance and operations

```text
clean-clone CI · example/boundary/property/metamorphic/mutation tests
Gold Corpus · cross-host agreement · NativeAOT execution smoke
Avalonia headless tests · parser/pack fuzzing · malicious container suite
SBOM/license report · reproducible manifest · signatures/attestations
security disclosure · rollback/revocation drill · capability/status check
```

### 35.7 · Channels

```text
NativeAOT CLI reference · Avalonia desktop workhorse
Avalonia mobile only after demand/proof · conditional Fable web inspector
stateless web check-and-go · installed local workspace
```

Flutter, Fable-Dart, Tauri, Electron, and a native verdict ABI are outside the
current GSTFlow platform decision. Reintroducing one requires an explicit ADR
showing a capability unavailable through Avalonia/.NET and proving that no
second verdict implementation is created.

### 35.8 · Accessibility, language, and community

```text
screen reader · keyboard · contrast · scalable text · large targets
reviewed language template packs · deterministic voice output
confirmed voice input · multilingual share artifacts
SEVA/community check-desk playbook · translation contribution lane
```

No account, directory, rating, or server marketplace is implied by the
community playbook.

### 35.9 · Registry and ecosystem

```text
pack discovery · signer/reviewer identity · trust level · compatibility
supersession · revocation · mirrors · offline snapshot · dispute record
competing reviewed interpretations · adoption/contact evidence
```

The term “marketplace” wakes only after governance, liability, revocation, and
dispute handling work. Before then it is a signed registry.

### 35.10 · Dormant features

```text
tamper-evident verdict hash chain · full mobile camera workflow
large on-device LLM · conversational explanation · voice NLU
Avro/Parquet/Arrow CFF payloads · multi-million-row performance claims
portal connectors · advanced refund/ISD/special-valuation families
```

Each dormant item has a wake condition: proven user demand, a named custodian,
a threat model, and an exit test. Dormant is not promised.

---

## 36 · EXECUTION PROGRAM

The sequence is gate-driven. Time estimates never override exit evidence.

### Gate 0 · Truth reset

```text
freeze surface expansion
restore clean-clone CI
remove stale projects/workflows and generated debris
classify every claim using §31
publish supported/unsupported matrix
name provisional custodians
```

**Exit:** a stranger can build the repository and understand what exists.

### Gate 1 · Trusted kernel

```text
explicit outcome precedence
reviewed money/rounding policy
canonical verdict envelope
typed uncertainty/evidence
no AI/native/UI logic in verdict path
JIT ↔ NativeAOT corpus agreement
```

**Exit:** the smallest kernel is deterministic, replayable, and green.

### Gate 2 · Assurance skeleton

```text
case and interpretation schemas
Crucible CLI
boundary/property/mutation suites
proof manifest
AI candidate-case workflow
fidelity model + semantic drift report
adapter/host conformance-kit skeleton
```

**Exit:** every engine change produces inspectable evidence.

### Gate 3 · One statutory vertical

Choose one narrow, high-value document journey.

```text
official/authoritative sources
CA author + independent reviewer
decision table + boundary family
Gold Corpus + mutations
sanitized real workflow
```

**Exit:** `Contact ≥ 1` and the professional reviewer will sign the record.

### Gate 4 · Canonical intake and Avalonia utility

```text
one official JSON adapter + one common ledger adapter
raw/parsed/normalized evidence
classified round-trip fidelity
confirmation workflow
single + batch preflight
plain verdict + provenance + export
Avalonia headless and AOT artifact tests
```

**Exit:** a user completes the North Star flow without developer help.

### Gate 5 · CFF v1

```text
canonical JSON evidence bundle
safe archive parser
digest/signature terminology
round-trip/replay/import/export
projection fidelity report
malicious-container suite
```

**Exit:** another clean machine reproduces the verdict from the bundle.

### Gate 6 · Class P rule packs

```text
pack schema · signing · trust store · expiry/staleness
supersession · revocation snapshot · rollback
HSN/rate/date/key/template parameters
```

**Exit:** a parameter change ships without engine rebuild and replays exactly.

### Gate 7 · Crucible-governed Studio

```text
parameter editor first
interpretation/case/review workflow
canonical normalization + contradiction diagnostics
pack diff and impact report
DSC/provider integration proof
Class D interpreter threat model and prototype
```

**Exit:** a non-programmer authors a constrained candidate; independent
reviewers approve it; Studio cannot bypass policy.

### Gate 8 · Authenticity and reconciliation

```text
real IRP QR known-answer vectors and key lifecycle
purchase register ↔ GSTR-2B first
immutable match graph and drill-down evidence
performance corpus and duplicate detection
```

**Exit:** pilot users confirm that exceptions are correct and actionable.

### Gate 9 · Broader rule families

Activate one family at a time from §8:

```text
POS expansion → RCM → rate/exemption → ITC screening
→ return reconciliation → exports/SEZ → advanced families
```

Each repeats Gate 3. No family inherits another family’s legal approval.

### Gate 10 · Web/mobile and registry

```text
Fable agreement before web verdict
mobile demand + camera/AOT/lifecycle proof
registry governance before marketplace language
offline trust snapshots and revocation drills
```

**Exit:** every activated channel passes the same canonical corpus and its own
platform threat model.

---

## 37 · RELEASE DEFINITION OF DONE

A feature is complete only when:

```text
Contract
∧ Implementation
∧ Red-run falsifier
∧ Boundary family
∧ Failure diagnostics
∧ Cross-host agreement where applicable
∧ Security/adversarial evidence where applicable
∧ Custodian approval
∧ Clean-clone CI
∧ Truthful documentation
∧ Rollback/revocation plan where applicable
∧ External contact for production claim
```

“Code merged” is not a completion state.

---

## 38 · LANDMINE REGISTER

| Landmine | Constitutional countermeasure |
|---|---|
| Scope expansion outruns trust | gated families and wake conditions |
| Law forced into binary answers | Unknown/NeedsEvidence/ProfessionalReview |
| Tests mistaken for legal proof | oracle hierarchy and independent review |
| Rule pack becomes malware | P/D/E classes, bounded interpreter, no arbitrary code |
| Decimal drift across hosts | managed reference and agreement corpus |
| FFI/native crash | peripheral isolation and soak/fault testing |
| Historical verdict changes | pinned replay and immutable packs |
| AI poisons oracle | CandidateCase workflow and human derivation |
| Marketplace signs bad law | governance, review identity, revocation, disputes |
| OCR invents facts | confirmation and raw evidence preservation |
| README becomes fiction | status law and evidence-linked claims |
| Founder becomes bottleneck | separated custodian roles and reviewer growth |
| Offline becomes stale | explicit freshness, signed human-carried updates |
| Privacy becomes lock-in | portable CFF/backup and explicit retention |
| Platform count fragments engine | one-engine law and channel activation gates |
| Generator silently drops meaning | classified fidelity and semantic drift gate |
| New driver copies old assumptions | executable conformance kit |
| Snapshot becomes self-fulfilling | missing/changed snapshots require explicit review |
| Agent sees approximate contract as truth | emitted agent context carries fidelity gaps |

---

## 39 · SUCCESS LAW

Vanity metrics are subordinate:

```text
downloads, stars, LOC, test count, speed benchmark < trusted contact
```

Primary measures:

```text
confirmed defects caught before action
false-verdict rate by governed family
Unknown resolved through requested facts
corpus mutations killed
cross-host agreement
historical replay success
time from reviewed parameter change to signed pack
external CA/business contacts
documented corrections and rollback time
```

The binding constraint remains:

```text
Contact ≥ 1 per activated statutory family
```

A mathematically elegant architecture with no independent user or reviewer is a
sealed room.

---

## 40 · IMMEDIATE ORDER

Before adding another major surface:

```text
1. Name provisional custodians.
2. Make clean-clone CI green.
3. Correct claims and publish the capability matrix.
4. Freeze the canonical verdict envelope.
5. Resolve outcome precedence and money policy with proofs.
6. Create ten independently derived Gold cases.
7. Expand them into boundaries, properties, and mutations.
8. Run them through JIT and the published NativeAOT binary.
9. Produce the first Crucible proof manifest.
10. Put one bounded workflow before one external CA and one real user.
```

Studio implementation begins only after its Rule IR, approval model, pack
classes, and Crucible evidence contract are frozen.

---

## 41 · CONSERVATION LAWS

```text
H = Duplication + Exceptions + SpecialCases + UnsupportedClaims

∀ PR : ΔH ≤ 0
```

```text
Min(Code) · Min(Coupling) · Min(PlatformsBeforeProof)
Max(Determinism) · Max(Evidence) · Max(Replay) · Max(Contact)
```

Weekly questions:

```text
What can be deleted?
What claim lacks evidence?
What uncertainty is being hidden?
What can crash outside the type system?
Who besides the author has challenged this?
```

---

## 42 · FINAL FORM

```text
CanonFlow = Reasoning + Evidence Infrastructure
GSTFlow   = CanonFlow + GST Knowledge + GST Context
Studio    = Governed Authoring
Crucible  = Executable Falsification
CFF       = Portable Reproducibility
Registry  = Governed Distribution
AI        = Candidate Generator, never Oracle
Custodian = Accountable Continuity
```

Therefore:

```text
Trusted GSTFlow
  = Exact Kernel
  ⊕ Canonical Facts
  ⊕ Reviewed Rules
  ⊕ Adversarial Tests
  ⊕ Signed Evidence
  ⊕ Honest Uncertainty
  ⊕ Independent Contact
```

> **The moat is not F#, Avalonia, Fable, NativeAOT, AI, DuckDB, or Avro.
> The moat is the accountable chain from authoritative source to reproducible
> verdict—and the discipline to stop whenever one link is missing.**

---

## 43 · IMPLEMENTATION REFERENCES

- [Original law-driven format: `mathsoverhaul.md`](https://github.com/ArunNotFound/direction_ai/blob/main/mathsoverhaul.md)
- [CanonFlow Manifesto and constitutional invariant](https://github.com/CanonFlowFoundation/CanonFlow/blob/main/MANIFESTO.md)
- [CanonFlow governance and kernel-promotion rule](https://github.com/CanonFlowFoundation/CanonFlow/blob/main/GOVERNANCE.md)
- [CanonFlow semantic optimizer ADR](https://github.com/CanonFlowFoundation/CanonFlow/blob/main/docs/ADR-015-Semantic-Optimizer.md)
- [CanonFlow drift-engine ADR](https://github.com/CanonFlowFoundation/CanonFlow/blob/main/docs/ADR-016-Drift-Engine.md)
- [CanonFlow honest-state and agreement tickets](https://github.com/CanonFlowFoundation/CanonFlow/tree/main/docs/tickets)
- [EDIFlow schema/profile/proof/fixture pattern](https://github.com/CanonFlowFoundation/EDIFlow/tree/main/awesome-edi-schemas/walmart/850)
- [GSTFlow source and adversarial rule tests](https://github.com/CanonFlowFoundation/GSTFlow)
- [FsCheck documentation](https://fscheck.github.io/FsCheck/)
- [Microsoft.Testing.Platform overview](https://learn.microsoft.com/en-us/dotnet/core/testing/microsoft-testing-platform-intro)
- [Avalonia testing guidance](https://docs.avaloniaui.net/docs/testing/)
- [Avalonia headless platform](https://docs.avaloniaui.net/docs/testing/setting-up-the-headless-platform)
- [.NET NativeAOT deployment guidance](https://learn.microsoft.com/en-us/dotnet/core/deploying/native-aot/)
- [.NET NativeAOT warning guidance](https://learn.microsoft.com/en-us/dotnet/core/deploying/native-aot/fixing-warnings)
- [Fable releases and numeric compatibility fixes](https://github.com/fable-compiler/Fable/releases)

These links inform implementation choices. This constitution, reviewed source
material, and recorded project ADRs govern CanonFlow product behavior.

Foundation synthesis reviewed on 2026-07-14 against:

```text
CanonFlow  4669b5f
GSTFlow    18fe5c0
EDIFlow    72f007b
docs       fdb37bc
site       97b1796
```

Repository prose and checklists are intention evidence, not implementation
proof. Only source, executable tests, CI, artifacts, and independent contact can
advance a capability to `PROVEN`.
