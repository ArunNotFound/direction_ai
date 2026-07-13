# GSTFlow — GitHub Actions Run 29235791438: Fixes

**Run:** [Build Flutter Apps (APK & EXE)](https://github.com/CanonFlowFoundation/GSTFlow/actions/runs/29235791438)  
**Head commit reviewed:** `51c900c`  
**Status:** Fix set prepared locally; commit, push, and rerun the workflow to verify on GitHub-hosted runners.

## What failed

| Job | Failure path | Root cause |
| --- | --- | --- |
| Build Android APK | Flutter test/build | The Flutter parser passed five arguments to generated `TaxAmount`, which accepts four. The test fixture also used an invalid buyer GSTIN checksum. |
| Build Windows EXE | Flutter test/build | A clean runner did not generate the Fable Dart runtime (`fable_modules`) required by Flutter imports. |
| Test Web Gateway (Playwright) | Playwright test | A clean runner did not generate the Fable TypeScript runtime. The test additionally clicked a nonexistent `Raw JSON` button. |

## Applied fixes

### Flutter correctness

- Removed the obsolete `StateCess` argument in `flutter_app/lib/invoice_parser.dart`; generated `TaxAmount` takes only `Igst`, `Cgst`, `Sgst`, and `Cess`.
- Replaced the invalid test GSTIN `29PQRSX9876L1ZV` with checksum-valid `29AAGCB7383J1Z4`.
- Added `crypto: ^3.0.3` as a direct Flutter dependency because the app and tests import it directly.

### Reproducible Fable generation

- In `.github/workflows/build-artifacts.yml`, added .NET/Fable setup and generation before:
  - Android Flutter build: `GSTFlow.Dart` → `flutter_app/lib/fable_dart`
  - Windows Flutter build: `GSTFlow.Dart` → `flutter_app/lib/fable_dart`
  - Web Playwright build: `GSTFlow.Wasm` → `playground/src/fable`
- Corrected `.github/workflows/build-android.yml` and `.github/workflows/release.yml` to build the `GSTFlow.Dart` project into the import path the Flutter app actually uses. The prior Core/Rules output paths were not consumed by the app.

### Playwright test alignment

- Removed the click on `Raw JSON`, which is not rendered by the UI.
- The test now verifies the visible `Govt JSON Playground` entry point; JSON mode is already the default.

## Files changed

- `.github/workflows/build-artifacts.yml`
- `.github/workflows/build-android.yml`
- `.github/workflows/release.yml`
- `flutter_app/lib/invoice_parser.dart`
- `flutter_app/pubspec.yaml`
- `flutter_app/test/engine_test.dart`
- `playground/e2e/invoice.spec.ts`

## Validation completed

- All edited workflow files parse as YAML.
- `git diff --check` passes for the fix set.
- Static checks confirm the four-argument `TaxAmount` call, valid GSTIN fixture, correct Playwright selector, and Fable generation commands.
- GSTIN checksum verification confirms the new fixture is valid and the replaced fixture is invalid.

## Verification still required on GitHub

This environment does not have Flutter or .NET installed, so full Android, Windows, and Playwright builds could not be executed locally. After committing and pushing, rerun the linked workflow and verify all three jobs complete successfully.

## Scope note

The pre-existing local change in `.github/workflows/ci.yml` was deliberately not modified.
