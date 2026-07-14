# AGY Handover: GSTFlow Windows 11 NativeAOT Testing 

**To my counterpart AGY (running on the Windows 11 host):**

Hello! I am your counterpart AGY running in the Linux VM where the majority of the kernel hardening and "Trust Reset" took place. We have successfully solidified the pure F# mathematical engine (`GSTFlow.Rules`) into a highly deterministic, air-gapped validator using 128-bit `System.Decimal`.

The user is now shifting to your session on the Windows 11 host to perform the "Boots on the Ground" testing phase, specifically to benchmark the NativeAOT bulk ingestion capability of `GSTFlow.Cli` and `GSTFlow.UI`.

**Your Mission:**
You are to guide and execute the Windows compilation, mock data generation, and batch testing of the CLI, and report the findings back to the user. We are ignoring the CI/CD pipeline failures for now; we are building natively on the host.

---

### Step 1: Pull the latest Code
The user's local Windows repository needs to be up-to-date with my Linux-side commits.
**Command for you to run/suggest:**
```powershell
cd path\to\GSTFlow
git pull origin main
```

### Step 2: Generate the 10,000 Mock Invoices
I created an F# script (`generate_10k_mocks.fsx`) that synthesizes 10,000 randomized JSON invoices designed to trigger our statutory checks (SEZ, inter-state, mathematical anomalies).
**Command for you to run/suggest:**
```powershell
dotnet fsi generate_10k_mocks.fsx
```
*(This will create a folder named `mock_10k_invoices` in the root of the repo containing 10,000 `.json` files. It is explicitly `.gitignore`'d so it won't bloat the repo).*

### Step 3: Build the Windows NativeAOT Executable
We are bypassing standard JIT to test the raw performance of .NET 10 NativeAOT. You must publish the CLI as a self-contained, native `win-x64` binary.
**Command for you to run/suggest:**
```powershell
dotnet publish GSTFlow.Cli/GSTFlow.Cli.fsproj -c Release -r win-x64 --self-contained true -o ./publish/cli
```

### Step 4: The 10k Batch Destruction Test
Run the native executable against the 10,000 JSON invoices. We expect it to process them incredibly fast and drop an `exceptions.csv` file inside the `mock_10k_invoices` directory listing all failures.
**Command for you to run/suggest:**
```powershell
.\publish\cli\GSTFlow.Cli.exe --validate-batch ./mock_10k_invoices
```

### Step 5: Validate and Analyze Results
1. Check how long the batch process took.
2. Read `mock_10k_invoices\exceptions.csv` to ensure the mathematical engine caught the anomalies as expected.
3. If everything is green, we will proceed to wire this exact ingestion logic into the Avalonia UI.

**Context for you (Windows AGY):** 
The user is here to *facilitate* and *human test*. Do the heavy lifting, run the commands, and parse the outcomes for them. If the build fails or acts weird on Windows, use your tools to fix it in place.

Good luck!
