Diagnosis

The latest CI failure is not NativeAOT, Flutter, Dart FFI, Wasm, Node, or Android packaging.

It fails during the ordinary F# solution build because GSTCanonicalIR was renamed from an Invoice field to SourceInvoice, but one CLI reference was missed.

The latest run, CI #90 at commit c4f97e0, reports:

GSTFlow.Cli/Program.fs line 60:
GSTCanonicalIR does not define a member named 'Invoice'

The run exits during dotnet build --no-restore, before tests, Fable compilation, playground tests, or any Android step execute. 

Exact cause

The current core type is:

type GSTCanonicalIR =
    {
        SourceInvoice: Invoice
        DerivedSupplyType: SupplyType
        PlaceOfSupply: StateCode
        IsInterstate: bool
    }



GSTFlow.Emit was correctly updated to use:

ir.SourceInvoice.InvoiceNumber
ir.SourceInvoice.Items



But GSTFlow.Cli/Program.fs still contains:

printfn "✅ Invoice %s validates successfully!" ir.Invoice.InvoiceNumber



That stale access is the immediate build breaker.

Immediate fix

Change this:

printfn "✅ Invoice %s validates successfully!" ir.Invoice.InvoiceNumber

to:

printfn "✅ Invoice %s validates successfully!"
    ir.SourceInvoice.InvoiceNumber

Commit:

fix(cli): use SourceInvoice after GSTCanonicalIR field rename

That should remove both duplicate compiler annotations reported for line 60.


---

Why the latest attempted fixes did not help

Two recent commits addressed unrelated layers:

1. .NET 10 Preview setup was adjusted.


2. Wasm was updated to expose violations.



But CI now successfully reaches the compiler and fails on the stale CLI field. Changing SDK configuration cannot repair a source-level member mismatch. The latest two CI runs show the same ir.Invoice compiler error. 

So this is a partial rename refactor, not a CI environment defect.

Refactor history

The repository history shows:

fix: Update GSTFlow.Emit to use renamed SourceInvoice property

But only the emitter was repaired. The CLI remained stale. 

This is precisely the sort of change that a solution-wide symbol search should catch.

Instructions for Gemini

GSTFlow CI Repair

Do not modify SDK versions, Flutter configuration, NativeAOT, Fable, or fixtures until the F# solution builds.

1. Repair the stale CLI field

In:

GSTFlow.Cli/Program.fs

Replace:

ir.Invoice.InvoiceNumber

with:

ir.SourceInvoice.InvoiceNumber

2. Search the entire repository

Search for all obsolete accesses:

git grep -n '\.Invoice'
git grep -n 'ir\.Invoice'
git grep -n 'GSTCanonicalIR'

Review every result manually.

Do not blindly replace "Invoice", because legitimate type names and raw invoice values exist.

The invariant is:

GSTCanonicalIR.SourceInvoice

No code should expect:

GSTCanonicalIR.Invoice

3. Validate locally in CI order

Run exactly:

dotnet restore
dotnet build --no-restore
dotnet test GSTFlow.Tests

Only continue if all three pass.

Then run the first positive fixture:

dotnet run \
  --project GSTFlow.Cli/GSTFlow.Cli.fsproj \
  -- --validate fixtures/fixture_1_intrastate_b2b.json

Then run the remaining CLI script represented in ".github/workflows/ci.yml".

4. Validate the emitters

Run:

dotnet run \
  --project GSTFlow.Cli/GSTFlow.Cli.fsproj \
  -- --emit-summary fixtures/fixture_1_intrastate_b2b.json

and:

dotnet run \
  --project GSTFlow.Cli/GSTFlow.Cli.fsproj \
  -- --prove fixtures/fixture_1_intrastate_b2b.json

Confirm both use "SourceInvoice" correctly.

5. Run browser compilation only after native build passes

dotnet tool restore
dotnet fable GSTFlow.Wasm \
  -o playground/src/fable \
  --lang typescript

cd playground
npm ci
npx tsx test_fable.ts

6. Commit only the bounded fix

Expected commit:

fix(cli): use SourceInvoice after GSTCanonicalIR rename

Do not combine it with:

- NativeAOT reintroduction;
- Flutter dependency upgrades;
- workflow redesign;
- fixture changes;
- serializer changes.

7. Add regression protection

Add a CLI smoke test that compiles and executes the successful validation path.

At minimum, ensure CI always runs:

dotnet build --no-restore

before any specialized jobs.

Prefer adding a test around the successful IR path so a future record-field rename cannot leave the CLI stale.

Exit criteria

dotnet build = pass
GSTFlow.Tests = pass
CLI fixture suite = pass
Fable compile = pass
JS/CLI agreement = pass
CI main = greenSecondary issue: Node warning

The Node message is currently only a warning:

actions/checkout@v4
actions/setup-dotnet@v4
actions/setup-node@v4
are running through Node 24 compatibility

It is not causing the failure; the workflow annotation separately identifies exit code 1 from the F# build. 

Deal with it after CI is green by upgrading to current major versions of the GitHub actions when those versions are available and verified. Do not confuse that warning with the build failure.

NativeAOT status

The repository currently reverted the NativeAOT Flutter experiment “in favor of Fable for Android stability.” That revert occurred immediately after the NativeAOT bridge commit. 

Therefore the current failing CI is testing the reverted Fable-based main branch, not the corrected NativeAOT architecture discussed earlier.

The correct sequence is:

1. Repair SourceInvoice.


2. Restore green baseline.


3. Create a separate NativeAOT branch or PR.


4. Add the safe length-aware ABI.


5. Prove Windows first.


6. Prove Android independently.


7. Merge only after both the existing CI and native-library jobs are green.



Fresh assessment

This failure is a healthy CI catch:

\text{IR schema changed}
\land
\text{consumer not changed}
\Rightarrow
\text{build rejected}

The problem is not the CI pipeline. The pipeline correctly detected an incomplete refactor.

The one-line source fix should unlock the next stage; only after it lands will any deeper fixture, Fable, or agreement failures become visible.
