# GSTFLOW-PLAN.md — Pre-Launch Plan (build quietly, launch gated)

> Thesis: GSTFlow is CanonFlow's distribution vehicle — the Tuesday-problem
> app that carries the semantic compiler to real users. Offline, free,
> no-catch GST validation for the underserved end of a mandatory market.
> **Nothing in this plan is a launch.** Launch has its own gate (G4) with
> criteria written now, executed only when earned.

## Standing boundaries (write into README before any code)
- **Validation only. Never transmission.** No IRP submission, no GSP API, no
  return filing in v1 — the GSP authorization wall is absolute and we stay
  on the free side of it. GSTFlow tells you your invoice is *right*; it never
  files it.
- **Data never leaves the browser.** The WASM architecture is the promise
  made mechanical. No telemetry, no accounts, no server in v1.
- **Not tax advice.** Rule-provenance to the public regulation, always; a
  disclaimer that this is a validation aid, not a CA.
- Standing item (parked, not closed): legal session covers this repo by name.

## Phase G0 — Foundations (now)
- [ ] **GSTIN as the first Refined type** — 15 chars, state-code prefix from
      the official vocabulary, embedded PAN structure, mod-36 check character.
      This is the flagship proof AND the demo's "oh" moment: check character
      validated offline in the browser. Law-first: valid GSTINs round-trip,
      single-character mutations are rejected (property: flip any char →
      invalid with probability from the check design).
- [ ] Laws for `normalize`: `IsInterstate ⟺ seller.State ≠ placeOfSupply`;
      totality over `Buyer` option; POS derivation stable.
- [ ] The IGST/CGST law — the domain's most beautiful invariant:
      `interstate ⟹ (Igst > 0 ∧ Cgst = 0 ∧ Sgst = 0)` and the intrastate
      dual, plus `Cgst = Sgst` always. Property-tested, transpiled, agreed.
- [ ] CI from day one (learn CanonFlow's lesson): laws + Fable agreement
      test in a workflow, witnessed green before module #2 exists.
- [ ] Kill the skeleton smell: delete unused stub modules (Agent, Adapters,
      Verify, Reconcile) — restore them when they have content. Empty
      modules are architecture theater; the repo should only contain what
      is true.

## Phase G1 — The Minimal Honest Rule Set
Scope: the smallest set that validates a real B2B invoice end-to-end.
- [ ] GSTIN (both parties) · state-code vocabulary (InSet) · HSN format
      (4/6/8 digits — format only; rate lookup is G2) · rate ∈ official
      slabs (InSet) · tax arithmetic (rate × taxable = tax, rounding rule
      stated once) · the IGST/CGST split law wired to `normalize`.
- [ ] Every rule carries provenance to the public source (rule name, notif.
      reference). The Layer Map discipline: which rules are format-checkable
      here vs which require live data (marked out-of-scope honestly, e.g.
      GSTIN *existence* requires the portal — we check *structure* only,
      and say so).
- [ ] Planted specimens, Layam-style: an invoice with a bad check character,
      a both-IGST-and-CGST invoice, an off-slab rate. Expected verdicts
      committed as fixtures.

## Phase G2 — The Playground Becomes the Product
- [ ] One flow, polished: paste invoice JSON (or fill a simple form) →
      verdict list, each line = rule + pass/fail + provenance + fix hint.
- [ ] Real branding: title, favicon, the no-catch promise stated ("free,
      offline, your data never leaves this page — view source").
- [ ] Remove Vite/React default assets. The page must never say
      "Vite + React + TS" anywhere a stranger can see.
- [ ] Hindi/Tamil verdict strings behind a toggle — the vernacular mission,
      cheaply, from day one (strings only, no i18n framework yet).

## Phase G3 — Quiet Validation (still not launch)
- [ ] **The falsifier: one real invoice from one real business, validated
      correctly, witnessed.** A CA contact or a small-business owner in your
      network — private, no announcement. Their confusion log is the backlog.
- [ ] Three real invoices from three sources → fixture corpus (anonymized).
- [ ] One CA reads the rule list and confirms no rule is *wrong* (wrong
      beats missing — a false verdict in tax context is the one unforgivable
      bug; this is Sangam S1's lesson with penalties attached).

## Phase G4 — Launch Gate (criteria written now, executed later)
Launch happens only when ALL of:
1. G3 falsifier passed (real invoice, real business, correct verdict).
2. CI green history exists (not claimed — visible).
3. A CA has reviewed the rule set for false-verdict risk.
4. The legal session (parked item) has happened and covered this repo.
5. CanonFlow's own stranger-demo has run at least once — the framework the
   app advertises must be presentable before the app points at it.
Launch form when gated: F# Weekly + one Indian dev community post. Not before.

## What GSTFlow deliberately is NOT (anti-sprawl fence)
No return filing · no reconciliation engine (GSTR-2B matching is a v2 label)
· no e-way bill · no accounts/ledger · no mobile app yet · no MCP/Agent
module until a user asks. Each is a `post-v1` label, not a folder.

## The relationship rule (protects both repos)
GSTFlow consumes CanonFlow patterns; it never grows framework features.
If a need arises that belongs in the framework (e.g., a new constraint
kind), it is filed as a CanonFlow issue and built THERE, dogfooded HERE.
The app hardens the framework; the framework never hides in the app.

*One real invoice, one real business, one correct verdict — everything else
is preparation for that moment.*

---

# PART II — Distribution Architecture (brainstormed, still pre-launch)

> Steward's directive: correctness first (nuts and bolts, no bells), then
> three modes — free web, packed native exe (Rust, NO Electron), Android.
> iOS deferred. Resolution: **one F# core, one web UI, thin Rust shells.**

## D0 · The One-Engine Principle (constitutional for GSTFlow)
Every mode runs the SAME Fable-compiled core. No mode gets its own rule
logic, ever. The law: **verdict agreement across all modes** — the golden
fixture corpus produces byte-identical verdicts in browser, desktop,
Android, and CLI, checked in CI, divergence = release blocker.
Known risk #1: money math. JS has no native decimal; Fable's Decimal
implementation must be agreement-tested against .NET on a corpus saturated
with rounding boundaries. GST's statutory rounding (Sec 170, rupee rounding)
is stated once, law-tested, applied everywhere.

## D1 · Mode 1 — Free Web (GitHub Pages + PWA)
- The existing playground, hardened per G2, deployed to Pages.
- PWA manifest + service worker → installable on Android TODAY, fully
  offline after first load. This is the interim Android story at zero cost.
- No accounts, no telemetry, no server. "View source" is the trust story.
- Sequencing note: deploy under the project org (post org-move) or accept
  one URL migration; custom domain (e.g. gstflow.dev) kills the problem.

## D2 · Mode 2 — Desktop exe (Tauri, the Rust answer; NO Electron)
- **Tauri 2.x**: Rust shell + OS-native webview hosting the same built web
  bundle. ~5–10MB signed binary vs Electron's 100MB+; memory-light; the
  Rust is the shell, F# stays the engine.
- Targets: Windows exe first (the CA-office OS), Linux AppImage second,
  macOS when convenient.
- File-system niceties via Tauri APIs: open/validate local JSON, batch a
  folder, export a verdict report (PDF later — bells; CSV now — bolts).
- **Binary trust (mode-2's special duty):** reproducible build from public
  CI, SHA-256 checksums in releases, GitHub build attestation/Sigstore.
  The downloaded-exe version of "your data never leaves this page."

## D2b · Mode 2½ — Native CLI (.NET NativeAOT) [added]
- `gstflow validate invoices/*.json` — single-file native exe of the same
  F# core, no runtime install. The batch tool for CA firms and scripts;
  also the reference oracle the agreement test diffs other modes against.
- Cheapest mode to build (it's the core + Argu), highest trust (it IS the
  .NET semantics, no JS decimal question).

## D3 · Mode 3 — Android (Tauri 2 mobile)
- Same web bundle in a Tauri Android shell → APK. Sideload + GitHub
  release first; Play Store only post-launch-gate (Play requires privacy
  policy, signing ceremony — bells until a user asks).
- PWA (D1) covers Android until the APK earns its build effort. Trigger:
  first real user asking for an installable app.
- Android-specific bolt: share-sheet intake ("share invoice JSON to
  GSTFlow") — one Tauri plugin, high utility, still not a bell.

## D4 · Mode ordering & gates
1. D0 agreement law wired (CI, all modes it can reach) — before any shell.
2. D1 web hardened (G2) — it is the demo AND the PWA-Android interim.
3. D2b CLI — cheap, and it becomes the verdict oracle for everything else.
4. D2 Tauri Windows exe — after G3's first real user validates the flow.
5. D3 APK — on first user demand; PWA until then.
6. iOS — a README saying "PWA works on iOS Safari today; native later."

## D5 · What stays OUT (bells, named so they stay out)
Auto-update frameworks · installers with wizards · analytics/crash
reporting · PDF report styling · themes · accounts/sync · Play Store
presence pre-launch · any per-mode feature that breaks the one-engine rule.

*Three doors, one engine, one law: the same invoice gets the same verdict
everywhere, provably. That is the whole distribution strategy.*

## D3b · iOS — re-included (the owner's pocket) [amended]
Rationale: Indian MSME *owners* skew iPhone even where staff/market is
Android. The trust decision-maker holds the excluded device — so iOS is in,
with eyes open about where its cost actually sits.

- **Served TODAY by D1:** the PWA installs from Safari (Share → Add to Home
  Screen) and runs offline. The iPhone-owner is covered the moment mode 1
  ships — zero extra build. State this on the site ("Works on iPhone: add
  to home screen").
- **Native later via the same engine:** Tauri 2 targets iOS — same web
  bundle, thin shell, one-engine law intact. The *build* is cheap; the
  *distribution* is the expensive part and is named honestly:
  - Apple Developer Program ($99/yr) · App Store review + privacy ceremony
  - macOS/Xcode required to build — no Mac on hand ⇒ **GitHub Actions
    macOS runners** do the iOS builds in CI (solved, free tier suffices)
  - No sideloading escape hatch (unlike Android APK) — App Store or nothing
- **Trigger:** first real iPhone-owning user for whom the PWA is not
  enough, AND post-launch-gate. Until then: PWA + one README line.
- Ordering update (D4): … 6. iOS PWA messaging ships WITH D1 · 7. native
  iOS shell last, on trigger.

*Amendment principle held: no new engine, no new rules surface — iOS is a
fourth door on the same house, and the cheapest door (PWA) is already open.*

---

# PART III — PDF Intake Workflow (read → extract → confirm → validate → layman fix)

> The real user has a PDF, not JSON. This is the intake that makes G3's
> real-business test natural — but it is v1.1, AFTER the form/JSON path
> ships, because extraction must never be the first thing we're wrong about.

## P0 · The two-layer law (constitutional for intake)
Extraction is PROBABILISTIC; validation is DETERMINISTIC. They never touch
directly. Between them stands the confirmation screen: extracted fields
shown, editable, confidence-flagged. The engine validates only what the
human confirmed. A mis-read digit must never become a confident verdict —
that is the false-verdict class with an upstream cause, and it is forbidden
by design, not by hope.

## P1 · The offline extraction stack (license-clean, in-browser)
- Digital PDFs → pdf.js (Apache 2.0): text + positions, client-side.
- Scans/photos → Tesseract.js (Apache 2.0, WASM): OCR, client-side, lazy-
  loaded only when needed (it's heavy — don't tax the simple path).
- Field mapping: heuristics keyed to Indian invoice conventions (GSTIN
  regex anchor, "Invoice No/Date" labels, HSN column, tax table shapes) —
  deterministic rules first; no cloud, no AI in v1 extraction.
- Every extracted field carries a confidence tag: Certain (regex-anchored,
  e.g. a GSTIN pattern) | Probable (label-matched) | Guessed (positional).
  Guessed fields are ALWAYS highlighted for human confirmation.
- The promise extends: the PDF never leaves the device. State it on the
  intake screen; it is the differentiator no upload-based incumbent can match.

## P2 · Confirmation screen (the human checkpoint)
- Side-by-side: PDF page render | extracted form, fields color-coded by
  confidence, tap-to-correct.
- One rule: the [Validate] button is disabled until every Guessed field is
  confirmed or edited. Certain/Probable may pass with a glance.
- Nothing persists. Closing the tab forgets everything (session-only).

## P3 · Layman verdict layer
- Every rule ID maps to a verdict template: **what's wrong → why it costs
  you (₹/consequence) → what to do** — plain words, zero jargon.
- Vernacular: template strings translated once (Hindi, Tamil first),
  toggle from day one. Templates, not machine translation at runtime.
- NO LLM in the verdict path. Deterministic templates only: testable,
  offline, incapable of hallucinating a wrong fix into a tax document.
- Verdict screen ends with a one-line summary the owner can forward:
  "2 problems found: supplier's GST number has a typo; tax charged at a
  rate that doesn't exist. Both fixable before filing."
- Fixture test: every rule ID has a template in every language, or the
  build fails (no verdict may ever surface as a bare rule code).

## P4 · Sequencing & scope fences
1. Form + JSON intake ships first (G2 as planned) — extraction rides on a
   proven engine, never the reverse.
2. pdf.js digital-PDF path second (most supplier invoices are digital PDFs).
3. Tesseract OCR path third, behind a "scanned invoice?" affordance.
4. OUT for v1.x: photos-of-crumpled-paper quality rescue, bulk-PDF batch
   (CLI's job), auto-learning layouts, any cloud/AI extraction assist.
   Each is a labeled later, not a silent gap.

*The engine proves; the extractor guesses; the human decides between them;
the verdict speaks the owner's language. Four layers, honest at every seam.*

---

# PART IV — Interop & Coexistence (bridge without burning hands)

## I0 · The prime bridge: government formats, not vendor formats
Primary intake = GSTN's own published schemas: INV-01 e-invoice JSON,
GSTR-1 JSON, e-way bill JSON. Every vendor must emit these; none owns them.
One bridge, all vendors, zero ToS exposure, zero per-vendor maintenance.

## I1 · Safe bridges (in order)
1. GSTN JSON intake (I0) — the universal adapter.
2. Excel/CSV importer with column mapping — the true lingua franca of
   Indian business; serves every tool and every CA without naming any.
3. Tally XML — accepted strictly as user-exported files (their data, their
   action). Version unrecognized → graceful "use Excel export" fallback.
Rule: ACCEPT EXPORTS, NEVER INTEGRATE. No vendor APIs, no cloud scraping,
no portal automation under user logins, no IRP (standing GSP wall).

## I2 · Non-burn marketing rules
- Never name competitors in product or copy. "Works alongside your existing
  accounting software." Compatibility mentions are minimal-nominative only.
- Position: the pre-flight check BEFORE whatever you file with. Not an
  alternative, not a replacement — the layer in front of all of them.
- Every verdict stays advisory: "confirm with your CA." The fatal burn is
  not a vendor's lawyer; it is one owner penalized by a wrong fix.

## I3 · The feature fence (against the alternates trap)
GSTFlow absorbs ONLY validation-layer capabilities incumbents structurally
cannot offer: rule provenance, offline verdicts, vernacular explanations,
the forwardable pre-flight report. Ledgers, inventory, filing, recon
dashboards = their war, our post-v1 labels. An alternate competes; a
conscience gets adopted by everyone — including their users.

## I4 · The zero-cost integration: the verdict report
One-page forwardable output (share/PDF): issues, plain-language fixes,
rule references. It flows through existing workflows by human hands —
owner → supplier → their Tally → CA. Write-access to nobody's system;
integration with everybody's, implemented as a share button.

*Speak the government's language, accept everyone's exports, name no one,
fix nothing by force — and let the verdict report walk through every door
the vendors locked.*
