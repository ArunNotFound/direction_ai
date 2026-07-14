# GSTFlow Deep Re-Review — 14 July 2026

## Executive verdict

**Repository:** [CanonFlowFoundation/GSTFlow](https://github.com/CanonFlowFoundation/GSTFlow)  
**Pinned commit:** [`278b7f1`](https://github.com/CanonFlowFoundation/GSTFlow/commit/278b7f1ddea178e9abb99951797cf61b78a62d36)  
**License:** Apache-2.0  
**Review scope:** source, architecture, statutory-rule safety, numeric fidelity, Avalonia migration, CFF/columnar/AI claims, tests, CI/CD, releases, security, and repository hygiene.

**Bottom line: all changes are not done.** The rewrite has made genuine progress, especially by establishing a directly managed Avalonia → F#/.NET path and proving that the Windows app and Linux NativeAOT CLI publish. However, GSTFlow is still an **alpha structural preflight prototype**, not yet a trusted statutory GST utility or production CFF implementation.

The strongest improvement is architectural: the dangerous Flutter/Dart decimal bridge is no longer used by the new Avalonia desktop app. The largest remaining risk is semantic: mixed `Fail` + `Unknown` results can produce an overall `Unknown`, allowing at least one API/CLI path to report success despite a real failure. Several newly documented “complete” features—CFF packaging, Parquet/Avro, DuckDB, QR verification, ONNX/Gemma, and zero-copy emission—are demonstrations, stubs, or declarations rather than working implementations.

### Standing

| Area | Previous standing | Current standing | Verdict |
| --- | ---: | ---: | --- |
| Architectural direction | 8/10 | 8/10 | Strong vision; scope still too broad |
| Implemented kernel | 3/10 | 4/10 | Better hashes/POS conservatism/tests; core outcome flaw remains |
| Avalonia desktop path | 0/10 | 5/10 | Real project and successful Windows publish; UI is synthetic demo |
| CFF / Avro / Parquet / DuckDB | 1/10 | 1/10 | Still mostly documentation and simulation |
| Statutory/legal assurance | 2/10 | 2/10 | No effective-dated, cited, CA-approved rule pack |
| CI/release health | 2/10 | 3/10 | Three new jobs pass, but all top-level product workflows remain red |
| Production readiness | 2/10 | **3/10** | Meaningful progress, still pre-production alpha |

## What genuinely went well

### 1. Direct managed Avalonia path is real

`GSTFlow.UI` is a real Avalonia 11.2 project targeting .NET 10 and directly references `GSTFlow.Core`, `GSTFlow.Rules`, and `GSTFlow.Emit`. The new workflow successfully published a self-contained Windows artifact. This removes Dart `double` conversion and native marshalling from the desktop verdict path.

This matches the binding GSTFlow direction:

- UI and verdict execution remain in managed .NET.
- Monetary fields remain `System.Decimal` in F#.
- NativeAOT is used as an application/CLI deployment format, not required as an in-process UI bridge.

Evidence: [`GSTFlow.UI.fsproj`](https://github.com/CanonFlowFoundation/GSTFlow/blob/278b7f1ddea178e9abb99951797cf61b78a62d36/GSTFlow.UI/GSTFlow.UI.fsproj) and the successful Avalonia job in [run 29301336633](https://github.com/CanonFlowFoundation/GSTFlow/actions/runs/29301336633).

### 2. Linux NativeAOT CLI publish is now proven

The new workflow successfully published the Linux NativeAOT CLI. That is a useful distribution milestone and much stronger evidence than a project setting or README claim.

Evidence: [`GSTFlow.Cli.fsproj`](https://github.com/CanonFlowFoundation/GSTFlow/blob/278b7f1ddea178e9abb99951797cf61b78a62d36/GSTFlow.Cli/GSTFlow.Cli.fsproj) and [run 29301336633](https://github.com/CanonFlowFoundation/GSTFlow/actions/runs/29301336633).

### 3. Real SHA-256 replaced the kernel placeholder

`Hash.computeSha256` now computes a real digest, and CLI input hashing uses the original JSON bytes. This resolves the former `hash_not_computed` defect in current .NET source.

Evidence: [`GSTFlow.Core/Library.fs`](https://github.com/CanonFlowFoundation/GSTFlow/blob/278b7f1ddea178e9abb99951797cf61b78a62d36/GSTFlow.Core/Library.fs).

### 4. Place-of-supply inference became more conservative

When POS is absent, the engine now emits `Unknown` instead of silently deriving POS from the buyer GSTIN. That is materially safer. Place of supply depends on facts that are not present in the current invoice model, so refusing to guess is correct.

Evidence: [`PlaceOfSupply.fs`](https://github.com/CanonFlowFoundation/GSTFlow/blob/278b7f1ddea178e9abb99951797cf61b78a62d36/GSTFlow.Rules/PlaceOfSupply.fs).

### 5. The test/build foundation is improving

The engine solution builds and 23 xUnit/FsCheck tests pass on GitHub. Tests now cover GSTIN structure, interstate/intrastate tax-family contradictions, missing POS, RCM invoice consistency, credit/debit references, IRN format, decimal aggregation, SEZ/export examples, and a deterministic SQL router.

That is useful regression protection, though it is not yet legal validation or a real gold corpus.

## P0 findings — must fix before any trust or production claim

### P0.1 — `Unknown` outranks `Fail`

`RuleOutcome` is declared as `Pass | Warning | Fail | Unknown`, then overall outcome is computed with F# `List.max`. F# comparison follows union-case order, so `Unknown` outranks `Fail`.

Observed consequence in committed fixtures:

- A document containing `TAX_AMOUNT = Fail`, `IGST_CGST_LAW = Fail`, and `PLACE_OF_SUPPLY_UNKNOWN = Unknown` has `OverallOutcome = Unknown`.
- `--emit-envelope` returns exit code 1 only when overall outcome equals `Fail`; therefore that failed document can exit **0**.
- Fable/Wasm calculates `success` only from the absence of `Fail` in one branch and treats warning/unknown inconsistently.

This is a release blocker. Define severity explicitly, for example `Fail > Unknown > Warning > Pass`, and test every combination rather than relying on DU ordering.

Evidence: [`RuleOutcome` declaration](https://github.com/CanonFlowFoundation/GSTFlow/blob/278b7f1ddea178e9abb99951797cf61b78a62d36/GSTFlow.Core/Library.fs), [`List.max` aggregation](https://github.com/CanonFlowFoundation/GSTFlow/blob/278b7f1ddea178e9abb99951797cf61b78a62d36/GSTFlow.Rules/Library.fs), and [`--emit-envelope`](https://github.com/CanonFlowFoundation/GSTFlow/blob/278b7f1ddea178e9abb99951797cf61b78a62d36/GSTFlow.Cli/Program.fs).

### P0.2 — ₹1 intrastate error can still pass

The engine allows each CGST and SGST component to differ by up to `₹0.50` because it fails only when the difference is `> 0.5m`. Therefore CGST can be wrong by ₹0.50 and SGST wrong by ₹0.50—a combined ₹1.00 discrepancy—and both checks pass.

`System.Decimal` removes binary floating-point drift; it does not make a permissive business tolerance exact. Replace the magic tolerance with an explicit, named, legally reviewed policy and add boundary tests for ₹0.01, ₹0.49, ₹0.50, ₹0.51, and combined CGST+SGST discrepancies.

Evidence: [`validateItem`](https://github.com/CanonFlowFoundation/GSTFlow/blob/278b7f1ddea178e9abb99951797cf61b78a62d36/GSTFlow.Rules/Library.fs). Microsoft’s [`Decimal` documentation](https://learn.microsoft.com/en-us/dotnet/api/system.decimal?view=net-10.0) also makes clear that decimal arithmetic still requires rounding decisions.

### P0.3 — Section 170 is still applied to the wrong base

The rule calculates `totalInvoiceValue = taxable value + taxes + cess` and warns when the entire invoice value is fractional. Section 170 concerns rounding amounts of tax, interest, penalty, fine, or other sums payable/refundable/due—not a general command to round the taxable base plus tax total on every invoice.

The current warning text and test canonize an overbroad interpretation. Remove this rule until a CA/legal reviewer approves the exact taxable concept, aggregation boundary, midpoint behavior, evidence, citation, and effective date.

Primary reference: [Central Goods and Services Tax Act, 2017](https://www.indiacode.nic.in/handle/123456789/15689).

### P0.4 — The repository has not completed the no-FFI/no-Flutter migration

The Avalonia desktop path is managed, but the solution still contains `GSTFlow.Native`, including `Marshal.PtrToStringUTF8`, unmanaged allocation/freeing, and `UnmanagedCallersOnly`. Old workflows still try to build the deleted `GSTFlow.Dart` and Flutter applications using a hand-written Dart `double` decimal shim.

Therefore the repository is not yet “100% Avalonia/.NET” as a whole. Remove the FFI project from the solution and delete or archive the obsolete Flutter/Dart workflows and residue. If the web playground remains, label it non-authoritative until agreement tests are green.

Evidence: [`GSTFlow.Native`](https://github.com/CanonFlowFoundation/GSTFlow/tree/278b7f1ddea178e9abb99951797cf61b78a62d36/GSTFlow.Native), [`GSTFlow.slnx`](https://github.com/CanonFlowFoundation/GSTFlow/blob/278b7f1ddea178e9abb99951797cf61b78a62d36/GSTFlow.slnx), and [obsolete artifact workflow](https://github.com/CanonFlowFoundation/GSTFlow/blob/278b7f1ddea178e9abb99951797cf61b78a62d36/.github/workflows/build-artifacts.yml).

### P0.5 — CFF “complete” claims are not implemented

Current code does not create a `.cff` ZIP, Avro file, Parquet file, attachment tree, schema, or verifiable signature. `generateCffManifestJson` returns a JSON string that declares files which are never created. The rule-pack digest is the fixed SHA-256 of an empty payload. `created_at` makes output non-deterministic.

`emitParquetBinaryBytes` explicitly simulates Parquet by returning `PAR1 + three bytes + PAR1`; this is not a valid Parquet implementation. There are no Avro, Parquet, Arrow, or DuckDB package references or schema files.

The README and standing report must call these designs/experiments, not completed pillars.

Evidence: [`CffPackager`](https://github.com/CanonFlowFoundation/GSTFlow/blob/278b7f1ddea178e9abb99951797cf61b78a62d36/GSTFlow.Emit/Library.fs), [`NativeColumnar.fs`](https://github.com/CanonFlowFoundation/GSTFlow/blob/278b7f1ddea178e9abb99951797cf61b78a62d36/GSTFlow.Emit/NativeColumnar.fs), and the [Apache Avro specification](https://avro.apache.org/docs/1.12.0/specification/).

### P0.6 — QR “signature verification” is a hard-coded success

`decodeOfflineQr` ignores its input and returns fixed invoice data with `SignatureVerified = true`. The UI/CLI then prints “100% offline signature verified.” This is a dangerous trust claim and must be removed until a genuine NIC payload parser, certificate/key trust chain, signature algorithm, negative vectors, and revocation/update policy exist.

Evidence: [`QrDecoder`](https://github.com/CanonFlowFoundation/GSTFlow/blob/278b7f1ddea178e9abb99951797cf61b78a62d36/GSTFlow.Emit/Library.fs).

## P1 findings — blocks credible beta

### P1.1 — “Comprehensive statutory decision tree” is overstated

The POS engine accepts an explicit state code and compares it with the seller state. It does not model goods versus services, movement/delivery/install location, bill-to/ship-to, immovable property, events, transport, telecom, banking, OIDAR, cross-border service facts, or notification/effective-date context.

For SEZ, the code marks a supply as zero-rated when either buyer **or seller** is SEZ. Inter-state classification covers supply “to or by” SEZ, but zero-rating is not automatically symmetric for every domestic outward supply by an SEZ. This needs legal correction and cited tests.

Treat current POS logic as a small consistency checker, not Sections 7/10/12/13 coverage.

Primary reference: [Integrated Goods and Services Tax Act, 2017](https://www.indiacode.nic.in/).

### P1.2 — RCM checks consistency, not statutory applicability

The engine trusts an invoice-level `ReverseCharge` string and checks that a marked RCM invoice does not collect tax. It does not prove that Section 9(3) applies, because that depends on effective government notifications, supplier/recipient category, goods/services, exemptions, and transaction context.

Rename the rule to describe what is actually known: “RCM flag/tax amount consistency.” Do not label it Section 9(3) determination until a versioned notification dataset exists.

### P1.3 — No real rule-pack governance

The envelope hard-codes engine/rule versions, omits a rule-pack digest, and leaves statutory references/effective dates empty. `computeHmacSha256` exists but is unused; HMAC is also not a public asymmetric signature.

Required contract:

- signed **data-only** rule pack;
- rule ID, source citation, notification number, effective-from/until;
- jurisdiction and taxpayer/document context;
- canonical rule-pack digest in every verdict;
- no executable code loaded from a rule pack;
- CA/legal approval record and revocation path.

### P1.4 — Evidence is not evidence-rich

Most results use an empty path, no value, no parameters, no legal reference, and `Provenance = Compiler`. The human message passed to `createRule` is discarded. This cannot support an audit trail or explain the financial impact.

### P1.5 — The new Avalonia UI is a benchmark simulator, not a utility

The UI generates 10,000 random synthetic rows; it has no file open/import path, no real CFF ingestion, no QR camera/parser, no DuckDB, no ledger, and no usable invoice workflow. Generated GSTINs do not have calculated check digits, so the benchmark largely measures early rejection of fabricated invalid parties.

The stopwatch stops after queueing UI updates, not necessarily after the grid finishes rendering. There is no BenchmarkDotNet project, dataset specification, warm/cold separation, memory measurement, or checked benchmark artifact.

Evidence: [`MainWindow.fs`](https://github.com/CanonFlowFoundation/GSTFlow/blob/278b7f1ddea178e9abb99951797cf61b78a62d36/GSTFlow.UI/MainWindow.fs).

### P1.6 — AI and DuckDB SQL are demonstrations

The “GBNF inference” is a keyword `if/elif` router that returns four fixed SQL strings and invented latency values. The Ollama branch supplies grammar in the prompt but does not enforce or parse/validate model output. ONNX inference is explicitly a stub returning `None`. DuckDB is not embedded or invoked.

Keep AI outside the verdict path, label the feature a demo, remove latency claims, and add a strict SQL AST allow-list before executing any generated query.

Evidence: [`SqlInference.fs`](https://github.com/CanonFlowFoundation/GSTFlow/blob/278b7f1ddea178e9abb99951797cf61b78a62d36/GSTFlow.Rules/SqlInference.fs) and [`LocalLlmClient.fs`](https://github.com/CanonFlowFoundation/GSTFlow/blob/278b7f1ddea178e9abb99951797cf61b78a62d36/GSTFlow.Rules/LocalLlmClient.fs).

### P1.7 — CI is improved but still red

At the pinned commit:

- New Trinity workflow: engine/tests ✅, Windows Avalonia publish ✅, Linux NativeAOT publish ✅, Fable build ❌.
- Legacy CI: fails during CLI fixture checks; agreement tests never run.
- Pages source build: Fable compilation fails.
- Flutter artifact workflow: obsolete and broken.
- No GitHub Release exists, despite tags `v1.0.0` and `v1.0.1`.

Evidence: [Trinity run 29301336633](https://github.com/CanonFlowFoundation/GSTFlow/actions/runs/29301336633), [CI run 29301336640](https://github.com/CanonFlowFoundation/GSTFlow/actions/runs/29301336640), and [Pages run 29301336665](https://github.com/CanonFlowFoundation/GSTFlow/actions/runs/29301336665).

The first legacy CLI specimen now fails because missing POS correctly produces `Unknown`, but the workflow still calls it a valid success specimen. Update specimens to state exact expected outcomes instead of expecting a zero exit code for ambiguous input.

## P2 engineering and governance debt

- **Stale generated output:** committed `out/` and generated playground code still contain `hash_not_computed`; current Fable generation is red.
- **Committed build debris:** about 180 tracked `bin/`, `obj/`, logs, IDE, local SDK, Playwright, and test-result files remain.
- **Unsafe JSON construction:** summary and manifest JSON use string interpolation without JSON escaping/canonical serialization.
- **CSV injection/escaping:** batch CSV does not consistently escape invoice numbers, GSTINs, filenames, or spreadsheet formula prefixes.
- **No date semantics:** invoice date is only checked for non-whitespace; no calendar parse, tax-period validation, or rule effective-date selection.
- **No package/release trust:** no lock file for NuGet transitive resolution, SBOM, `THIRD_PARTY_NOTICES`, signed artifacts, provenance/attestation, or reproducibility proof.
- **No project governance:** no `SECURITY.md`, `CONTRIBUTING.md`, code of conduct, CODEOWNERS, threat model, or responsible disclosure path.
- **Dependency risk:** a lockfile-only npm audit reported one moderate and two low production dependency findings in the playground tree; this needs triage, not concealment.
- **No external validation:** no CA/legal sign-off and no `Contact >= 1` proof artifact from a real external user/accountant.

## Claim-versus-reality matrix

| Claim | Reality at `278b7f1` | Safe wording |
| --- | --- | --- |
| “Pure F# deterministic kernel” | Mostly true for current rules; inputs/context and severity contract remain incomplete | Deterministic prototype rules kernel |
| “128-bit exact; zero drift” | `System.Decimal` is used, but rounding/tolerance policy is wrong/incomplete | Decimal arithmetic with policy still under validation |
| “100% Avalonia/.NET” | New desktop path yes; repository still contains FFI, Fable, and obsolete Flutter workflows | Avalonia desktop prototype with direct managed kernel |
| “CFF cryptographic container complete” | No ZIP, schema, actual payload files, or asymmetric signature | Draft manifest experiment |
| “Parquet/Avro zero-ingestion” | Fake `PAR1` bytes; no serializer or packages | Planned columnar storage research |
| “DuckDB ledger/query” | SQL strings only; no DuckDB dependency/execution | DuckDB-oriented SQL examples |
| “Offline signed QR verified” | Hard-coded result, input ignored | UI mock; verification not implemented |
| “Gemma/ONNX edge AI implemented” | ONNX stub; optional localhost Ollama; fixed fallback router | Experimental local inference adapter |
| “23/23 proves statutory correctness” | Tests pass, but several assert the same contested model | 23 implementation regression tests pass |
| “Production CLI” | NativeAOT publishes, but CI specimens fail and envelope semantics are unsafe | NativeAOT CLI alpha |

## Recommended evolution

### Gate 0 — trust reset

1. Replace DU `List.max` with explicit outcome precedence and exhaustive combination tests.
2. Fix the ₹0.50-per-component tolerance and specify midpoint/scale/aggregation rules.
3. Remove or quarantine the current Section 170 rule pending CA/legal approval.
4. Remove hard-coded QR verification and simulated Parquet from product-facing paths.
5. Downgrade README/standing claims to implemented, experimental, and roadmap labels.

### Gate 1 — finish the Avalonia utility

1. Remove `GSTFlow.Native`, deleted Flutter/Dart workflow references, and residue.
2. Make Avalonia the only authoritative end-user GSTFlow application.
3. Add real JSON/ZIP import, validation result list, evidence drill-down, and deterministic export.
4. Keep web as documentation/non-authoritative until canonical agreement is proven.
5. Add Windows smoke tests and offline network-denial tests.

### Gate 2 — canonical verdict contract

Define `VerdictEnvelope v1` with:

- parser status and source format;
- canonical subject digest plus optional raw-source digest;
- engine ID/version/build SHA;
- rule-pack ID/version/digest/signing key ID;
- ordered results with severity, evidence path/value/provenance, parameters, citation, and effective dates;
- explicit overall severity function;
- canonical JSON rules: decimal format, ordering, Unicode normalization, timestamps, and hashing.

Prove semantic and canonical agreement across CLI and Avalonia before adding another channel.

### Gate 3 — legally governed rule packs

1. Model facts separately from determinations.
2. Convert statutes and notifications into signed data-only packs.
3. Build a CA-reviewed corpus containing positive, negative, ambiguous, amended, and effective-date boundary cases.
4. Make `Unknown` a first-class request for missing evidence, never a hidden pass.
5. Publish machine-readable proof manifests for each release.

### Gate 4 — implement CFF for real

Start with one narrow, testable format:

1. frozen CFF specification and threat model;
2. canonical `manifest.json`;
3. one versioned Avro schema using a documented `decimal(28,4)` representation;
4. actual ZIP writer/reader with path traversal, duplicate-name, size, and decompression-bomb defenses;
5. asymmetric signature and trust-store model;
6. golden vectors and tamper tests in another implementation;
7. only then add Parquet/DuckDB projections and benchmarks.

## Release decision

### What may be released now

- Source-only **alpha**.
- Windows Avalonia **prototype/demo** artifact.
- Linux NativeAOT CLI **alpha** artifact.
- Clear disclaimer: structural/arithmetic preflight only; not GST filing approval, tax advice, or statutory certification.

### What must not be claimed yet

- production ready;
- comprehensive statutory validation;
- legally proven or CA-approved;
- CFF/Avro/Parquet/DuckDB implemented;
- cryptographically signed CFF or verified NIC QR;
- zero-copy/zero-ingestion performance;
- 100% offline proof;
- mobile Pro/Lite availability;
- byte-identical multi-runtime verdicts.

## Final assessment

GSTFlow has moved from **an ambitious multi-runtime prototype** to **a more credible managed-.NET desktop/CLI alpha**. That is real progress. The correct next move is not another feature phase. It is a short trust-hardening phase that makes one Avalonia utility truthful, deterministic, legally bounded, and green in a clean clone.

If the P0 items are fixed and the claims are reset, production readiness can move from 3/10 to roughly 5/10. A credible public beta requires the canonical verdict contract, CA-reviewed effective-dated rules, real CFF implementation, green release CI, and signed reproducible artifacts.

---

# Appendix A — Latest P0 Revalidation and Ordered Next Steps

**Revalidation date:** 14 July 2026  
**Previous review baseline:** `278b7f1`  
**Latest commit inspected:** [`30cab89`](https://github.com/CanonFlowFoundation/GSTFlow/commit/30cab89fc195fb8279b06e3474011bc25bef8e0f)  
**Intermediate remediation commit:** [`296439a`](https://github.com/CanonFlowFoundation/GSTFlow/commit/296439a3866cc8767bf6d6c9b5d7671da4b677a7)

This appendix supersedes status checkmarks added to the earlier review. A checkmark is not proof of closure. An item is closed only when the implementation, boundary tests, clean-clone CI, public wording, and dependent channels agree.

## A.1 Corrected P0 status

| Finding | Latest change | Verified status | Reason |
| --- | --- | --- | --- |
| P0.1 — `Unknown` outranks `Fail` | `RuleOutcome` reordered to `Pass, Warning, Unknown, Fail` | **🟡 Functionally corrected, not robustly closed** | `List.max` now yields `Fail`, but correctness still depends on source declaration order; no exhaustive precedence or permutation test exists |
| P0.2 — ₹1 intrastate error can pass | Replaced two `0.5m` component checks with combined `GST_ROUNDING_TOLERANCE = 1.0m` | **🔴 Open and regressed** | The code fails only when combined difference is `> 1.0m`; exactly ₹1.00 still passes. Interstate IGST and cess errors of exactly ₹1.00 also pass |
| P0.3 — Section 170 misapplication | Removed runtime Section 170 invoice-total rule | **🟡 Runtime rule removed; product surface not clean** | CLI showcase and documentation still advertise a Section 170 scenario/coverage |
| P0.4 — no FFI/no Flutter migration | Deleted `GSTFlow.Native` source and one Flutter workflow | **🔴 Incomplete** | `GSTFlow.slnx` still references the deleted Native project; Android and release workflows still invoke Flutter/Dart; Flutter residue remains tracked |
| P0.5 — simulated CFF/Parquet | Replaced simulated functions with `NotImplemented` exceptions | **🟡 Dangerous simulation removed; capability remains unimplemented** | This is an honest disablement, not implementation. README/docs still describe CFF, Avro, Parquet and DuckDB as built |
| P0.6 — hard-coded QR verification | Replaced hard-coded success with `NotImplemented` | **🟡 False verification removed; feature remains unimplemented** | Product/docs still claim offline signed QR verification |

### P0.1 technical judgment

The latest union order makes the current reducer behave as intended:

```fsharp
type RuleOutcome =
    | Pass
    | Warning
    | Unknown
    | Fail
```

However, this is a fragile implicit contract. A harmless-looking future reorder can silently reintroduce the defect. Closure requires an explicit severity function:

```fsharp
let severity = function
    | Pass -> 0
    | Warning -> 1
    | Unknown -> 2
    | Fail -> 3

let overall outcomes =
    outcomes
    |> List.maxBy severity
```

Better still, hide aggregation behind one tested `RuleOutcome.combine` function and forbid channels from reimplementing it.

Required proof:

- all 16 pairwise combinations;
- `Fail + Unknown = Fail` in both orders;
- aggregation is invariant under result ordering;
- CLI, Wasm and Avalonia consume `Envelope.OverallOutcome` rather than deriving local success;
- explicit exit/status policy for `Pass`, `Warning`, `Unknown`, and `Fail`.

### P0.2 technical judgment

Current code states:

```fsharp
let GST_ROUNDING_TOLERANCE = 1.0m
...
if cDiff + sDiff > GST_ROUNDING_TOLERANCE then Fail
```

Consequences:

- `cDiff = ₹0.50` and `sDiff = ₹0.50` produces exactly ₹1.00 and passes;
- an IGST difference of exactly ₹1.00 passes;
- a cess difference of exactly ₹1.00 passes;
- the README claim “We catch ₹1 discrepancies” is false at the inspected commit;
- “Explicit legally reviewed policy” is unsupported because no approval artifact or source is linked.

`System.Decimal` solves binary representation drift. It does not justify a ₹1 acceptance window.

Recommended rule contract:

1. Normalize the computed amount and declared amount to the legally selected scale using an explicit midpoint policy.
2. Compare normalized paise exactly for the structural arithmetic rule.
3. If business practice permits a separate rounding adjustment, model that as an explicit field/rule and emit `Warning`/`Unknown` with evidence—never silently convert the discrepancy to `Pass`.
4. Keep any configurable tolerance in a versioned, cited rule pack; do not embed an unexplained constant in code.

Minimum boundary suite:

| Scenario | Required outcome |
| --- | --- |
| Exact match | Pass |
| ₹0.01 component difference | Not Pass unless a cited rule explicitly permits it |
| ₹0.49 difference | Not silently Pass |
| ₹0.50 difference | Explicit policy outcome |
| ₹0.51 difference | Fail/Check according to cited policy |
| CGST ₹0.50 + SGST ₹0.50 | Must not Pass |
| IGST exactly ₹1.00 wrong | Must not Pass |
| Cess exactly ₹1.00 wrong | Must not Pass |
| Same numbers in different result/item order | Same outcome |

## A.2 Current build condition

At `30cab89`, the repository is not build-green:

- [Trinity run 29302675197](https://github.com/CanonFlowFoundation/GSTFlow/actions/runs/29302675197): engine, Avalonia, NativeAOT and Fable jobs all failed.
- [CI run 29302675185](https://github.com/CanonFlowFoundation/GSTFlow/actions/runs/29302675185): failed during restore; no tests or agreement checks ran.
- [Pages run 29302675207](https://github.com/CanonFlowFoundation/GSTFlow/actions/runs/29302675207): Fable compilation failed.

Static blockers visible in source include:

1. `GSTFlow.slnx` references deleted `GSTFlow.Native/GSTFlow.Native.csproj`.
2. `PlaceOfSupply.fs` uses `isB2b` in the SEZ branch before declaring it in the later domestic branch.
3. `GSTFlow.Wasm/Library.fs` reads `EstimatedLatencyMs`, which was removed from `SqlInferenceOutcome`.
4. Android/release workflows still reference deleted Flutter/Dart inputs.

No P0 or P1 item should be marked fully closed while clean-clone CI is red.

## A.3 Corrections to the online status labels

The following “fixed” labels in the online review are not supported at `30cab89`:

- **P1.3 rule-pack governance:** only a mock digest string was added: `0xMockDigestPendingGovernanceP1_3`.
- **P1.4 evidence-rich verdicts:** messages are now retained, but statutory references/effective dates remain empty and parameters remain empty.
- **P1.7 CI fixed:** contradicted by the three failed workflow runs above.
- **Generated/build debris fixed:** approximately 180 tracked build/log/IDE/test-result files remain.
- **Unsafe JSON fixed:** summary JSON still uses direct string interpolation.
- **CSV escaping fixed:** invoice number, GSTIN, filenames and formula prefixes are still not safely encoded.
- **Date semantics fixed:** date validation still checks only non-empty strings.
- **NuGet lockfiles added:** no `packages.lock.json` files are present.
- **Governance fixed:** `SECURITY.md`, `CONTRIBUTING.md`, CODEOWNERS, code of conduct, SBOM and third-party notices are absent.
- **External validation fixed:** documenting that validation is missing does not satisfy `Contact >= 1` or CA/legal sign-off.

## A.4 Next steps — execute in this order

### PR 1 — restore repository integrity

Scope this PR to deletion/build repair only:

1. Remove the stale Native project entry from `GSTFlow.slnx`.
2. Move `isB2b` above all branches that use it.
3. Remove `EstimatedLatencyMs` from Wasm output or restore the field consistently.
4. Delete/disable obsolete Flutter Android, Flutter release and Pake workflows.
5. Remove tracked Flutter residue, `bin/`, `obj/`, logs, IDE metadata, generated Fable output, Playwright reports and test results.
6. Run a single authoritative clean-clone pipeline: restore → build → test → NativeAOT publish → Avalonia publish.

**Exit gate:** every required job is green from a clean checkout. No agreement or test step is skipped.

### PR 2 — close P0.1 and P0.2 with proofs

1. Introduce explicit `RuleOutcome.severity/combine`; stop using DU comparison as policy.
2. Add exhaustive outcome-combination and ordering properties.
3. Remove the `1.0m` tolerance.
4. Introduce a named, versioned money/rounding policy with explicit scale and midpoint behavior.
5. Add the complete paise/₹1 boundary matrix above.
6. Make CLI, batch, Wasm and Avalonia use the same envelope status and the same success/exit policy.
7. Add golden JSON envelopes for mixed `Fail + Unknown`, `Warning + Unknown`, and exact ₹1 discrepancies.

**Exit gate:** the original falsifiers fail before the fix and pass after it; exactly ₹1 can never produce `Pass`.

### PR 3 — truth reset across public surfaces

1. Replace every stale `✅ FIXED` with `Verified`, `Partially addressed`, `Disabled`, `Open`, or `Roadmap` based on evidence.
2. Update README and standing reports to match current code.
3. Remove “green CI”, “production”, “complete”, “zero penalties”, “word-for-word CGST”, “QR verified”, and implemented CFF/DuckDB/Avro/mobile claims.
4. Label CFF, QR, Parquet, ONNX and mobile as unavailable until implemented.
5. Replace stale links pinned to `278b7f1` with links to the verification commit/run.

**Exit gate:** every capability claim has a source file, executable test, and green workflow artifact.

### PR 4 — canonical verdict envelope v1

1. Replace mock rule-pack digest with a real digest over canonical data-only rule-pack bytes.
2. Add engine build SHA, parser status, canonical subject digest and optional raw-source digest.
3. Populate evidence path, expected value, actual value, calculation and provenance.
4. Populate statutory source, notification, effective-from/until and jurisdiction context.
5. Define canonical JSON ordering, decimal text, Unicode normalization and timestamp rules.

**Exit gate:** CLI and Avalonia emit the same canonical semantic envelope for every golden specimen.

### PR 5 — make Avalonia a useful GST preflight utility

1. Replace the synthetic 10,000-row generator as the primary UX.
2. Add local JSON import, batch folder import, results, evidence drill-down and deterministic export.
3. Show `PASS / CHECK / FIX`, missing facts and supported-check scope.
4. Add offline/network-denial tests and Windows smoke tests.
5. Keep probabilistic AI outside the verdict path.

**Exit gate:** a CA can import a redacted invoice set, reproduce the CLI verdicts and export an evidence report without network access.

### PR 6 — legal and external validation

1. Create a real `Contact >= 1` record with consent and redacted evidence.
2. Obtain CA review of the money policy, RCM consistency wording, POS scope and rule outcomes.
3. Build an effective-dated corpus with positive, negative and ambiguous cases.
4. Record disagreements as `Unknown`/unsupported rather than forcing rules.

**Exit gate:** signed review record, reviewed corpus version and traceable changes exist.

### Later — implement CFF, QR and columnar storage independently

Do not combine these into the trust-hardening PRs. Each needs its own specification, threat model, parser/writer, negative vectors, resource limits, cryptographic trust model and interoperability tests. `NotImplemented` is currently safer than a convincing simulation.

## A.5 Definition of “fixed” for future reviews

Use this five-part gate for every checkmark:

1. **Implementation:** no stub, mock digest, hard-coded success or simulated output.
2. **Falsification:** boundary and negative tests demonstrate the old defect.
3. **Agreement:** every authoritative channel produces the same semantic result.
4. **Clean CI:** required jobs pass from a fresh checkout and publish the intended artifact.
5. **Truthful documentation:** claims describe only the verified scope and link to proof.

Until all five pass, use **partially addressed**, **disabled**, or **open**—not **fixed**.
