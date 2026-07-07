# ARANGETRAM.md — The Standing Challenge Program

> அரங்கேற்றம் — the debut performance judged live by unforgiving critics.
> CanonFlow performs it nightly, forever. Three engines, in escalating order
> of discomfort: the parser, the mathematics, the thesis. Each has a ratchet —
> a metric that may never move backward — which is what makes the challenge
> sustainable rather than a one-off torpedo run.

---

## Engine 1 — The Generator Gauntlet (discomforts the parser & the laws)

Hand-planted specimens (Layam, Sangam) test what we thought of. The gauntlet
tests what we didn't: **property-based schema generation with known ground
truth.**

**Mechanism.** An FsCheck `Gen<Schema>` produces random but valid schemas:
tables, columns over the type zoo (NUMERIC of random precision, quoted
CamelCase and reserved-word identifiers, NULLable at random), and — the key —
**constraint ASTs generated in our own lattice first**, then rendered to SQL.
Apply to a Testcontainers Postgres. Harvest back. Because the generator knows
the ground-truth AST, four meta-laws become checkable that no hand specimen
could reach:

1. **Deparse round-trip** — parse(pg_get_constraintdef(render(ast))) must be
   `equivalent` to ast, or classify as Opaque. Postgres's deparser is now our
   adversarial rewriter: every cast, paren, and quote it inserts is a free
   mutation of our test input. (This engine would have found S3 and S6
   before Sangam did.)
2. **Soundness** — generate 100 random rows; every row the DB *accepts*, every
   emitted validator (F#, TS, Kotlin) must accept. Forbids invented
   constraints — S1's law, generalized.
3. **Completeness accounting** — lifted + Opaque = pg_constraint count,
   exactly. Forbids silent loss. (Would have found S5's domain blindness:
   domains make the ledger not balance.)
4. **Constitutional round-trip** — emit(introspect(db)) redeployed to a fresh
   container, introspected again, `≅` with classified loss.

**Ratchets.** Every failing case is shrunk, committed to
`tests/gauntlet/seeds/` as a named regression, and appended to SURPRISES.md —
the corpus only grows. Nightly cron (Ornith-A1 runs it; Hermes reports).
A generator dimension, once added (domains, composite FKs, BETWEEN, enums,
partial indexes…), may never be removed. The gauntlet gets harder
monotonically; that is the sustainability.

## Engine 2 — The Corpus Gauntlet (discomforts the taxonomy with reality)

Random schemas lack the pathologies of history. Real ones have nothing else.

**Mechanism.** A fixture set of real open-source schemas, harvested and
version-pinned: an ERP with rich DB constraints, a Rails-lineage schema
(constraints deliberately thin in the DB), a Django-lineage one, something
ancient and Hungarian-notated. Nightly: introspect each, compute the
**Liftability Index** — proofs / (proofs + Opaque + invisible-known) — and
the soundness spot-check.

**Ratchets.** Per-schema index may never drop (regression = red CI). Every
Opaque *category* observed in the corpus gets a row in the U2 liftability
taxonomy — the taxonomy is now empirically fed, not speculative. Publish the
index table in the README: it doubles as radical-honesty marketing
("CanonFlow currently lifts 74% of Odoo-class constraints; here is exactly
what it cannot lift and why").

## Engine 3 — The Silent Schema (discomforts the thesis itself)

The deepest discomfort, aimed at the pitch, not the parser: **most real
enterprise schemas keep their constraints in the application layer.** Rails
validations, Django validators, service-layer if-statements — the database
is a bag of nullable VARCHARs. Against such a schema CanonFlow's harvest is
nearly empty, and the question is whether the product tells the truth.

**Mechanism.** One dogfood app, `silent-schema/`: a realistic order-management
DB with almost no CHECK constraints, accompanied by an app-layer validation
file that encodes twelve rules the DB never sees. Pass criteria:

- CanonFlow reports its own low yield *prominently* — "3 proofs from 40
  columns; this schema keeps its rules elsewhere" — not buried in a summary.
- The README's marketing claims survive contact: no sentence in MANIFESTO or
  the Advent draft becomes false when pointed at this schema. If one does,
  the sentence changes, not the schema.
- The diagnosis becomes the feature: the honest report of a silent schema is
  itself the sales artifact for the *greenfield* direction ("your rules live
  in code where no one can prove them; here is what emit-first would give
  you"). Discomfort converted to positioning — but only after it has been
  fully felt.

**Ratchet.** Any new marketing sentence, anywhere, must pass the Silent
Schema read-through before publication. The thesis stays falsifiable forever.

---

## Standing orders
- Engines run nightly in this order; total budget 30 CI minutes (Engine 1
  sized to fit — it is the one that scales).
- A red engine blocks release tags, never daily merges.
- Quarterly: the steward reads the seed corpus end-to-end — the accumulated
  shrunk failures are the truest history of what the material taught us.

*The arangetram is never passed once; it is performed again each night,
before critics that only grow sharper. That is the point.*
