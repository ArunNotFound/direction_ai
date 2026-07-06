---
name: law-driven-development
description: Use when implementing any CanonFlow feature or fix, before writing implementation code. Property law first; watch it fail.
tier: 1-process
triggers: [implement, feature, fix, new module, fscheck]
---

# Law-Driven Development

Superpowers TDD, elevated one level: for algebraic code the unit of truth is
a **property law**, not an example test.

Core principle: **if you didn't watch the law fail, you don't know it
constrains anything.**

1. **State the law** as an FsCheck property tied to the spec
   (Spec-ref required): lattice laws (absorption, De Morgan, complement,
   normalization idempotency), Refined round-trips, FieldClass classified-loss,
   driver conformance, cross-runtime F#↔TS agreement.
2. **Watch it fail** (red) against a stub. A law that passes immediately is
   either vacuous or already implied — investigate before proceeding.
3. **Minimal implementation** to green. No speculative generality (YAGNI).
4. **Shrink study:** when FsCheck shrinks a counterexample, record the shrunk
   case as a named regression property — that is the material talking back.
5. Example-based tests are permitted only for I/O edges (drivers, CLI) and
   must justify why a property was not possible.

Exceptions require steward sign-off: throwaway spikes in labs/ only.
Adapted-from: superpowers test-driven-development (MIT, Jesse Vincent).
