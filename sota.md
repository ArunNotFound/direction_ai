# SOTA Plan — CanonFlow as the EDI Constraint Substrate

> Corrected thesis: the spec-reading moat is cracking (incumbents admit AI cut
> their 7–10 day mapping to 2–3), but the EDI moat is THREE layers — data
> validation, wire transport, and bilateral certification. CanonFlow
> commoditizes ONLY the first, and should say so. The pure "schema replaces
> Zenbridge" claim dies on master-data mapping (a CHECK constraint cannot know
> Walmart's item number = your SKU). We win the validation/contract layer,
> integrate the rest, and let open-source erode the specs.

## 0. What we are NOT claiming (honesty as strategy)
- NOT an EDI VAN/transport replacement (AS2/SFTP/X12 envelope stay).
- NOT a full onboarding platform (certification/testing with Leaders remains).
- NOT "delete Zenbridge." We delete the *proprietary mapping-DB* moat, the
  narrow slice AI genuinely commoditized — and make it a public good.
This restraint is the moat against our OWN euphoria and against a reviewer's
first objection. It also keeps ADR-007 daylight: general constraint tooling,
not an EDI platform product adjacent to any employer system.

## 1. The wedge (precise, defensible)
**Claim:** a retailer EDI spec is a constraint document; CanonFlow turns
constraints into typed, provenance-carrying, multi-runtime validators — so the
*validation and contract* layer of EDI onboarding becomes a compile step
instead of a proprietary database.

Pipeline:
```
Retailer EDI 850 PDF ──LLM──► walmart_850.sql (Postgres, strict CHECKs)
                                    │
                        CanonFlow introspect
                                    │
        ┌──────────────┬───────────┴────────────┬─────────────┐
   Refined F# types  JSON Schema/OpenAPI   TS/Kotlin/Swift   PROOF.md
                                                validators    (fidelity+provenance)
```
The differentiator vs a raw LLM: **classified loss + proofs**. An LLM emits a
schema and a validator with equal, unwarranted confidence. CanonFlow emits a
validator that says which rules are Exact, which are Opaque, and where each
came from — the honesty an EDI compliance context legally requires.

## 2. The three-layer truth (put in the README, not hidden)
| Layer | Who owns it | CanonFlow's role |
|---|---|---|
| **Data validation & contract** | was Zenbridge's mapping DB | **REPLACE** — this is the wedge |
| **Wire transport** (AS2/SFTP, X12 envelope, 997s) | VANs, existing libs | **INTEGRATE** — call OpenAS2, a X12 parser; never rebuild |
| **Bilateral certification & master-data mapping** | the enterprise + the Leader | **ASSIST, not replace** — surface mismatches as classified diffs |

The master-data problem (Walmart item# ≠ your SKU) is where we are HONEST:
CanonFlow can *detect and classify* the mismatch (it's a drift/fidelity
problem — our home turf), but the mapping decision is human/bilateral. We make
it visible and typed; we do not pretend it's automatic.

## 3. The X12 gap — the one real build (scoped hard)
A CHECK-constraint schema models EDI *data*, not the X12 *envelope*. Two options:
- **A (recommended, in-charter):** treat X12↔row as a Provider. `EdiSegmentProvider`
  reads an X12 850 into the same canonical model Postgres introspection produces
  — reusing the entire lattice/Refined/emitter spine. The 850's element rules
  (mandatory/optional, min/max length, code lists) ARE constraints; they lift
  exactly like CHECKs. This is CanonFlow's contract arm pointed at X12 instead
  of SQL. ~1 provider, not a platform.
- **B (reject for v1):** full X12 read/write/envelope/997 engine. That's an EDI
  library (many exist, some OSS) — integrate, don't rebuild. post-v1.

## 4. The open-source play (the actual disruption)
`awesome-edi-schemas` — but designed correctly, learning from the euphoric
version's flaw. Not just `walmart_850.sql`, but a **triple** per partner:
```
walmart/850/
  schema.sql          # LLM-drafted, HUMAN-certified (a bad schema poisons users)
  profile.md          # the three-layer map: what's DB, what's transport, what's bilateral
  proof.md            # CanonFlow fidelity report — what lifts Exact vs Opaque
  fixtures/           # sample valid+invalid docs (the certification asset)
```
Governance is the moat here: an uncertified schema is worse than none (a
wrong CHECK = a rejected shipment = a chargeback). So the repo needs the same
conformance/specimen discipline as Layam/Sangam — a schema merges only with
fixtures proving it accepts valid and rejects invalid docs. **The community
commoditizes the specs; CanonFlow's laws keep the commodity trustworthy.**
That trust layer is what a raw `awesome-edi-schemas` of LLM output could never
have — and it's defensible precisely because it's the honesty engine, not the
schemas.

## 5. Why this beats a raw LLM (the durable differentiator)
An LLM alone gives you a schema you must trust blindly. The stack gives you:
- **Proof of fidelity** — every rule classified, provenance to the spec clause.
- **Multi-runtime agreement** — the same 850 rule rejects identically in the
  warehouse's Kotlin app and the portal's TS, property-tested.
- **Drift detection** — when Walmart revises the 850 spec, re-introspect and
  get a classified diff (Widened/Narrowed) instead of a silent break.
- **The agentic angle** — an AI agent onboarding a partner reads CanonFlow's
  provenance-carrying proofs as ground truth instead of re-reading the PDF.
The LLM is the eraser; CanonFlow is the *certifier*. Erasers are commodities;
certifiers are infrastructure.

## 6. Sequencing (respecting the constitution)
This is a post-v1 MARKET direction, not a reordering of the core. Gate on it:
- **Now:** nothing changes. Finish the three governance debts (CI, artifacts,
  ADRs). EDI is a `post-v1` label with this plan attached.
- **After v1.0 (round-trip law green on 4 drivers):** build `EdiSegmentProvider`
  (§3A) as ONE new provider + one dogfood: `edi-850-walmart/` with planted
  specimens (a spec ambiguity → Opaque; a code-list → InSet; a master-data
  field → flagged bilateral). Same specimen methodology, new domain.
- **Then:** seed `awesome-edi-schemas` with 3 certified partners + the
  fixture/conformance harness. Advent post's second audience: EDI teams.
- **Never (this venture):** transport engine, VAN, certification platform.

## 7. Risks specific to this thesis
- **Euphoria risk:** believing the schema replaces the stack. Mitigated by §2
  being public and §0 stating the non-claims first.
- **Bad-schema liability:** a wrong community schema causes a real chargeback.
  Mitigated by the fixture-certification merge gate — no fixtures, no merge.
- **Incumbent response:** they have transport + certification + relationships;
  they'll add AI mapping (TrueCommerce already did). We don't win their game —
  we commoditize one layer beneath them and let their customers self-serve it.
- **ADR-007:** keep it general constraint tooling with an EDI example, personal
  time/hardware, no employer-adjacent framing.

*The moat didn't evaporate — it shrank to one layer, and that layer wants to
be a public good. CanonFlow doesn't storm the castle; it makes the castle's
walls buildable by anyone, and keeps them honest with laws.*
