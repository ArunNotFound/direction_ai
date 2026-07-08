# GPT Review II — Cherry-Picked Intake (steward-validated)

> Rule applied: take the insight, refuse the platform. Every ACCEPT is baked
> into an existing CanonFlow mechanism so it adds language/wiring, never a new
> runtime. Every REJECT names why and where it goes instead.

## ✅ ACCEPTED — with the in-charter form spelled out

### A1 · "Semantic compiler, not code generator" → manifesto language
GPT's reframe is correct and matches our lineage work. BAKE: adopt the
compiler-pipeline framing already latent in the repo —
`Parse → Normalize (NNF) → Optimize (ADR-015 simplify) → Verify (laws) → Emit`
— as the MANIFESTO's opening spine. We already HAVE all five stages; we just
never named them as a pipeline. Zero new code; a naming that makes the
architecture legible and recruits the "very few schema tools think like
compilers" distinction as positioning.

### A2 · Structural Truth vs Business Behaviour → the honesty boundary, formalized
GPT: "DB owns *amount>0*; app owns *approve loan*." This is ADR-001 stated
better. BAKE: it's the same three-layer map the Mudra dogfood already tests
(db-enforced / profile-declared / unenforced). Promote it from a dogfood
detail to a first-class output: every introspection emits a LAYER MAP naming
which rules the schema enforces vs which live above it. My baked-in extension:
this is also the EDI plan's master-data honesty (Walmart item# ≠ SKU is a
"business behaviour" the DB can't own) — one concept serves manifesto, Mudra,
and the SOTA-EDI wedge simultaneously.

### A3 · Explainability → wire Lineage.fs, it already exists
GPT's "minimum=18 ← CHECK(age>=18) ← customer.age" is exactly Lineage.fs,
still under-wired. BAKE: this is already NEXTSTEPS T5. Elevate its priority —
GPT independently confirming it means it's load-bearing. My baked-in framing:
provenance is not a nice-to-have, it's the SOUNDNESS AUDIT TRAIL — the same
mechanism that lets a human debug ALSO lets the agent trust, AND lets the EDI
certifier trace a validator back to the spec clause. Three audiences, one
feature.

### A4 · Verification-first → yes, and we have the instrument
GPT: "invest in verification, not emitters." Already the constitution. BAKE:
nothing new to decide — but use GPT's phrasing "every constraint traceable"
as the acceptance bar for the Arangetram gauntlet's completeness law
(lifted + Opaque = total, each with provenance). Confirmation, not change.

### A5 · The agent-context INSIGHT (not the graph service) → the PUSH model
GPT is right that agents waste context rediscovering constraints. BAKE the
insight in CanonFlow's existing form: the provenance-carrying emitted
artifacts (Domain.fs comments, catalog JSON, PROOF.md) ARE the agent context,
delivered by PUSH (pre-emitted files the agent reads) — which our own Load
Line guide argued beats PULL. My baked-in deliverable: an `agent-context.json`
emitter — one more projection of the canonical model, listing per-entity
{constraints, nullability, enums, references, provenance, which-validators-exist}.
Static file, zero runtime, reads as ground truth. This captures 90% of the
"AI Sidecar" value at 0% of the platform cost.

## ❌ REJECTED — insight kept, architecture refused

### R1 · "Semantic Graph rather than Files" → post-v1 label, and probably never
A queryable graph is a runtime service — server, query language, availability
SLA — the largest scope expansion proposed in the project, dressed as focus.
Violates ADR-009 (build-time codegen, not runtime). The VALUE (relationships
as context) is captured by A5's static file carrying a `references` field per
entity. If graph-shaped queries are ever truly needed, they run over the
emitted file, not a live CanonFlow server. Post-v1 at most.

### R2 · "AI Sidecar as biggest COMMERCIAL opportunity" → wrong product, right value
A live service Cursor/Copilot query before editing = a runtime dependency,
an uptime promise, a different company. We deliver the same value as A5's
push artifact committed to the user's repo — offline, versioned, free. The
commercial opportunity stays the SOTA-EDI wedge (certified constraint
substrate), not a sidecar SaaS. Reject the product; keep the value via push.

### R3 · The implicit "broaden toward platform" gravity → the standing refusal
GPT's review, like its first, preaches depth while sketching breadth. The
standing answer: nothing broadens until the round-trip law is green on four
drivers (v1.0 gate). Semantic graph, sidecar service, twenty emitters — all
post-v1 labels. The constitution is unamended by an eloquent review.

## Net baked intake (what actually changes)
1. MANIFESTO gains the five-stage compiler framing (A1) — naming only.
2. Layer Map becomes a first-class output (A2) — promotes existing Mudra logic.
3. Lineage.fs wiring (A3) elevated in priority — already T5, now flagged core.
4. New `agent-context.json` emitter (A5) — one projection, static, captures
   the sidecar value in-charter.
5. Everything runtime/graph/service → post-v1 (R1–R3).

Four of five are naming/promotion of things that already exist. Exactly one
new artifact (agent-context emitter), and it's a file, not a server. That
ratio — mostly-legibility, one-small-build, zero-platform — is the signature
of correctly cherry-picked feedback.

*Take the insight, refuse the platform. The reviewer finds gems and smuggles
castles; the steward keeps the gems and leaves the castles as labels.*
