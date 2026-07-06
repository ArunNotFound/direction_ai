---
name: fsharp-identity-law
description: Load before writing or reviewing ANY F# code. Non-negotiable style law — Haskell/OCaml discipline; C# patterns are defects.
tier: 0-protected
triggers: [f#, fsharp, .fs, fsproj]
---

# F# Identity Law

<!-- PROTECTED -->
Parse, don't validate. Types as proofs. DUs over classes. Result over
exceptions. Immutability default; mutation only at audited boundaries.
Smart constructors — invalid states unconstructible. Phantom types for
compile-time capability. Writer monad for telemetry. Property-based tests
(FsCheck) over example tests for algebraic code.

Defect classes (flag ⚠️, never merge silently):
- Classes where a DU or record suffices; inheritance hierarchies
- null / exceptions for control flow; try/catch where Result belongs
- Mutable state outside audited boundaries; `for` loops where fold/map reads better
- C#-style method chains on mutable builders; `Nullable<T>` leakage
- Validation that checks-then-passes-raw instead of parsing into a refined type

Generated code additionally must be Fantomas-clean and carry no TODO stubs
presented as complete.
<!-- /PROTECTED -->

When uncertain whether a pattern is C#-flavored: it is. Rewrite functionally
or escalate with the ⚠️ flag and both versions.
