# GSTFLOW-SOTA.md — Production Overhaul (executor-ready, self-contained)

> For Gemini / any executor. The F# rules engine is the crux and it EXISTS
> (verified GSTIN mod-36, property laws, tri-mode Fable/AOT). This plan does
> NOT touch the crux. It steps up everything AROUND it to production grade:
> branding, UI/UX, all platforms, trust infrastructure. Full speed ahead on
> BUILD; public launch remains gated (Part E). Build like it ships tomorrow;
> ship when the locks turn.

---

# PART A — EXECUTOR BRIEFING (binding)

1. **One-Engine Law (constitutional):** every mode — web, Windows, macOS,
   Linux, Android, iOS, CLI — runs the SAME Fable-compiled F# core. No mode
   gets its own rule logic, ever. Golden fixtures must produce byte-identical
   verdicts across all modes in CI; divergence = release blocker. The AOT
   CLI is the reference oracle all modes diff against.
2. **No `|| true`, anywhere.** Every CI check must be able to fail. Specimen
   fixtures ASSERT expected verdicts (bad checksum → GSTIN_CHECKSUM fires,
   both-taxes → IGST_CGST_LAW fires). A test that cannot fail is not a test.
3. **No LLM in the verdict path.** Layman explanations come from
   deterministic templates keyed to rule IDs, translated once (EN/HI/TA).
   Build fails if any rule ID lacks a template in any shipped language.
4. **The GSP wall:** validation only. No IRP submission, no GSTN API calls,
   no filing. If a feature needs government API access, it is out of scope.
5. **Privacy is architecture, not policy:** no accounts, no telemetry, no
   network calls in the verdict path. Data never leaves the device — and the
   UI must PROVE it (Part C3). Errors get a "copy details" button, not
   crash reporting.
6. **Never name competitors** in product or copy. Positioning: the pre-flight
   check that works alongside whatever you already use.
7. **Verdicts stay advisory:** every screen that shows a verdict carries
   "confirm with your CA" in the active language.
8. **Style law for any F# touched:** DUs over interfaces, Result over
   exceptions, parse-don't-validate, dense functions. Rust appears ONLY as
   Tauri shell code; TypeScript ONLY as UI glue. Neither contains rules.

---

# PART B — BRANDING (decided; execute, don't re-litigate)

- **Product name: GSTFlow** — already the repo, descriptive, ownable.
  Consumer surface leads with the promise, not the name.
- **Tagline (EN):** "Catch the costly mistake before you file."
- **Tagline (HI):** "फाइल करने से पहले, गलती पकड़ें।"
- **Tagline (TA):** "தாக்கல் செய்யும் முன், தவறைக் கண்டுபிடி."
- **The trust triad, stated everywhere, in this order:**
  Free forever · Works offline · Your data never leaves your device.
- **Visual identity:**
  - Verdict colors carry the brand: deep green (PASS), amber (CHECK THIS),
    signal red (FIX BEFORE FILING). No other saturated colors compete.
  - Typography: one humanist sans with excellent Devanagari + Tamil support
    (Noto Sans family) — same voice in every script.
  - Motif: the rupee-paisa precision mark (₹ with a checkmark counter) as
    favicon/app icon. No mascots, no clip-art, no stock imagery.
  - Dark-first palette (shops are dim; screens are bright), AA contrast min.
- **Voice:** a knowledgeable neighbor, not a portal. Short sentences.
  Money consequences, not tax jargon. Never scold the user; the mistake is
  the supplier's or the software's, the fix is theirs to forward.

---

# PART C — DESIGN ESSENCE (the UX constitution; details are free within it)

## C1 · One Screen, One Answer
The entire product is: **give invoice → get verdict.** No dashboard, no
navigation tree, no onboarding. The home screen IS the drop zone (paste
JSON / upload PDF / photo / Excel). The verdict list replaces it in place.
Anything that adds a second concept to the home screen is rejected.

## C2 · Speak Money, Not Tax
Every finding renders as: **what's wrong → what it costs you → what to do.**
"Wrong GST number — your buyer loses ₹1,800 input credit — ask supplier to
correct and reissue." The ₹ consequence is computed where possible, ranged
where not. Rule IDs and law references live behind a tap ("why?"), never
on the surface.

## C3 · Trust Must Be Visible (the signature innovation)
Don't claim privacy — demonstrate it:
- A persistent **offline badge**: "✈ Working offline — nothing leaves this
  device." Toggle airplane mode in the demo; the app keeps working.
- A **"what left this device: 0 bytes"** counter in the footer (it reads
  the actual network log — and it is provable because there ARE no calls).
- **View-source link** on the web version; **checksum + reproducible-build
  link** on desktop binaries.
- Every verdict card shows **provenance on tap**: rule → the public
  regulation reference → the engine version that evaluated it.

## C4 · The WhatsApp-Shaped Verdict (the distribution UX)
India's business medium is WhatsApp. The verdict must export as a clean,
self-explanatory **share image** (and PDF): supplier name, invoice number,
the 1–3 findings in plain language + ₹ impact, a "checked by GSTFlow —
free, offline" footer. One tap: share sheet. The owner forwards it to the
supplier; the supplier's CA sees it. **The share card IS the marketing.**
Design it like it will be seen by ten thousand people who never opened
the app — because it will be.

## C5 · Confirmation Before Judgment
PDF/photo intake always shows the extracted-fields screen (confidence-
colored: certain/probable/guessed) before validating. The Validate button
stays disabled until every guessed field is confirmed. A misread digit
must never become a confident verdict.

## C6 · One Hand, Dim Light, Any Script
Thumb-reachable primary actions; minimum 48dp targets; readable at arm's
length in a dim shop; language toggle (EN/HI/TA) permanently visible on the
home screen, switching EVERYTHING including verdicts instantly.

---

# PART D — PLATFORM MATRIX (one engine, thin shells, zero heavy deps)

| Channel | Shell | Notes |
|---|---|---|
| Web + PWA | none (static hosting) | First to ship. Installable on Android AND iPhone (Safari → Add to Home Screen) day one. Service worker = full offline. |
| Windows | **Tauri 2** (Rust, OS webview) | ~5–10MB signed exe. The CA-office build. NO Electron, no runtime installs. |
| macOS | Tauri 2 | Same bundle; built on GitHub Actions macOS runners. |
| Linux | Tauri 2 AppImage | Same bundle. |
| Android | Tauri 2 mobile → APK | GitHub-release sideload first; Play Store post-launch-gate. Share-sheet intake ("share invoice to GSTFlow"). PWA covers until demand. |
| iOS | PWA now; Tauri iOS later | Native shell only post-launch-gate + real demand ($99 + review ceremony noted). |
| CLI | .NET NativeAOT | `gstflow validate *.json` — batch for CAs; THE verdict oracle for CI agreement. |

Binary trust for every desktop/mobile artifact: reproducible builds from
public CI, SHA-256 checksums in releases, GitHub build attestation.

---

# PART E — PRODUCTION-READY CHECKLIST (build now) & LAUNCH GATE (unchanged)

## Build now (full speed)
- [ ] Purge every `|| true`; specimens assert expected verdicts in CI.
- [ ] Agreement suite: fixture corpus → all modes → byte-identical verdicts;
      AOT CLI as oracle. Rounding fixtures saturate Section-170 boundaries.
- [ ] Rule-pack versioning: every rule carries an ID, effective date, and
      regulation reference; the verdict envelope records rule-pack version.
- [ ] Template completeness gate: every rule ID × every language, or build fails.
- [ ] The home screen (C1), verdict cards (C2), trust surface (C3),
      share card (C4), confirmation flow (C5), a11y pass (C6).
- [ ] Perf budget: verdict in <2s on a mid-range Android; PDF extract <5s.
- [ ] Landing page = the product (PWA) + trust triad + checksums. No Vite
      defaults anywhere a stranger can see.
- [ ] CF support peek (one afternoon): confirm GSTFlow needs NOTHING from
      CanonFlow to ship v1 (engine is standalone — expected answer: nothing
      blocking). Log the future hooks: FactsTableEmitter when persistence
      arrives; agent-context emitter when agent integrations arrive. If a
      genuine general gap surfaces, file it on CanonFlow per the routing
      fork — do not build it here.

## Launch gate (unchanged from GSTFLOW-PLAN; five locks, all required)
1. One real business validates one real invoice correctly (witnessed).
2. A CA reviews the rule list for false-verdict risk.
3. CI green history visible (with the specimens actually enforcing).
4. The legal session has covered this repo by name.
5. CanonFlow's stranger-demo has run once.
Build like it ships tomorrow. Ship when these turn. No exceptions, no
matter how good the share card looks.

*The engine is proven. This plan makes it presentable, provable, and
forwardable — so that when the first real shop owner meets it, every pixel
already tells the truth the lattice does.*

---

# PART F — VERNACULAR ROLLOUT (data-driven; supersedes the EN/HI/TA default)

> Principle: language priority follows the GST economy (state collections as
> invoice-volume proxy) BLENDED with MSME density (the actual user), not
> founder preference. Honest caveat baked in: high collection ≠ marginalized
> density — Maharashtra's rank is corporate-driven; UP ranks #6 in collection
> but #1 in registered taxpayers. Both indices matter.

## F1 · Rollout tiers (each language = one template pack; architecture unchanged)
| Tier | Languages | Coverage logic |
|---|---|---|
| T0 (ship v1) | English, **Hindi** | Hindi alone covers 4 of top-10 states (UP, Haryana, Delhi, Rajasthan) + national fallback. Highest users-per-template-pack. |
| T1 (fast follow) | **Marathi, Gujarati, Tamil** | #1 (₹3.6L cr), #3 (₹1.35L cr), #4 (₹1.30L cr) states. Marathi is the single largest gap in the old plan. |
| T2 | **Kannada, Telugu, Bengali** | #2 (₹1.58L cr), #9, #8 states — Bengaluru/Hyderabad trade + Bengal's MSME belt. |
| T3 (on demand) | Urdu, Punjabi, Bhojpuri, Odia, Malayalam… | Wake condition: user requests from that region, or a community translator volunteers. |

## F2 · Mechanism (why this is cheap)
- Every rule ID maps to a template per language; UI strings are one JSON
  pack per language. Adding a language = translating N strings ONCE.
- Build gate unchanged: every shipped language must cover every rule ID or
  the build fails. A language ships complete or not at all.
- **Community translation as the contribution onramp:** a language pack is
  the perfect first PR — no F#, no rules knowledge, high mission impact.
  Provide a `translations/TEMPLATE.md` with the string inventory and a
  glossary (GSTIN, input credit, IGST stay untranslated as terms of art).
- Native-speaker review required before a pack ships (a wrong vernacular
  verdict is worse than an English one — same false-verdict class).

## F3 · The share card is multilingual too
The WhatsApp verdict card (C4) renders in the active language — a Marathi
shop owner forwards a Marathi card to a Marathi supplier. The distribution
loop stays vernacular end-to-end; that is where "for the marginalized"
becomes mechanically true rather than aspirational.

---

# PART G — ECOSYSTEM MODES & FEATURE ADDITIONS (absorbed with fences)

> Principle: ONE product, three MODES — never three products. The individual
> mode's One-Screen-One-Answer constitution (C1) is inviolate; everything
> batch/workflow-shaped lives behind the Facilitator/CA toggle. All free,
> forever, all modes.

## G1 · The three modes
| Mode | Who | Surface |
|---|---|---|
| **Individual** (default) | shop owner | Unchanged: give invoice → verdict. Nothing added to this screen, ever. |
| **Facilitator** | the helper at a trade association, seva volunteer, accountant's assistant | Batch intake (folder/zip/multiple PDFs), the verified/pending workspace (G3), print + zip export. |
| **CA** | chartered accountants | Facilitator features + CLI pointer, rule-pack version pinning, bulk verdict-report export (per-client zip). |
Mode is a toggle in settings — no accounts, no roles server-side (there is
no server). A mode is a UI arrangement, not a permission.

## G2 · Voice (the mission feature)
- **G2a Voice OUTPUT first (T0 of voice):** every verdict card gains a
  speaker button — reads the layman verdict aloud in the active language
  (EN/HI/TA per Part F tiers). Use on-device TTS (Android/iOS system TTS;
  Web Speech synthesis where offline-capable). Low-literacy users hear
  "इस बिल में GST नंबर गलत है — supplier से ठीक करवाएँ।" This single button
  is the strongest "for the marginalized" feature in the product.
- **G2b Voice INPUT later, fenced:** spoken field entry routes through the
  confirmation screen (C5) at GUESSED confidence — a misheard GSTIN digit
  must never become a confident verdict. Numbers are read back digit-by-
  digit for confirmation before validation. Wake condition: G2a shipped
  and a real user asks for input.

## G3 · The verified/pending workspace (persistence, done deliberately)
- Local-ONLY storage (device IndexedDB / app storage). No sync, no cloud,
  ever — there is no server to sync to.
- The trust surface (C3) gains the sibling truth: "Stored on this device
  only · delete anytime" with a one-tap wipe-all.
- States: Pending → Verified / Needs-fix (with the share card ready).
- **Architecture note (binding):** this is the FactsTableEmitter wake
  condition firing — the storage schema is EMITTED from the F# verdict
  types (id, payload hash, verdict envelope, validated_at), never hand-
  authored. File the wake against CanonFlow per the routing fork; the
  schema arrives from the emitter, the app only consumes it.

## G4 · Action-loop features (small, high value)
- **Tap-to-call supplier:** verdict card carries the supplier phone (from
  the invoice or user entry) as a `tel:` action — "the fix is one call
  away." Zero infrastructure.
- **Print:** system print of the verdict report (Facilitator/CA modes).
- **Share as zip:** batch verdict cards + PDFs exported as one zip
  (client-side, JSZip) for a CA's client folder.
- **Scan-to-verify:** the existing camera/PDF path (C5), promoted to a
  first-class home-screen affordance in all modes.

## G5 · The free-service community loop (kept lightweight)
The ecosystem play, without marketplace machinery:
- A `SEVA.md` in the repo: how a facilitator/CA can run a free invoice-
  check desk (trade association hall, weekly market) using Facilitator
  mode — a one-page playbook, not a platform.
- Community translation packs (Part F2) as the parallel contribution lane.
- NO directory, NO listings, NO accounts, NO ratings — the moment the
  ecosystem needs a server, it has left this product's constitution.
  If demand for coordination emerges, it lives in community spaces
  (a Telegram/WhatsApp group run by the community), not in the app.

## G6 · Doubts registered (steward asked; answered honestly)
1. Persistence changes the privacy story — resolved by local-only + visible
   "stored here only / wipe all" + emitted schema (G3). Acceptable.
2. Voice input mishearing numbers — fenced to confirmation-screen flow (G2b).
3. Ecosystem sprawl — fenced to modes-not-products (G1) and playbook-not-
   platform (G5). The line that must never be crossed: any feature that
   requires a server. That is the constitution's edge, and every "add more
   as desired" gets tested against it first.

---

# PART G — AMENDMENTS (steward refinements, absorbed)

## G3-revised · Storage: installed apps ONLY (simplification, supersedes G3 scope)
- **Web/PWA stays 100% stateless.** Close the tab → nothing exists. This is
  the purest form of the privacy demo and it stays untouched. No IndexedDB,
  no browser-storage caveats, no common-denominator compromises.
- **Workspace (pending/verified) exists ONLY in installed apps** (Tauri
  desktop, Android APK, later iOS) using native app storage. The platform
  matrix gains a row-level truth: web = check-and-go; installed = check-
  and-keep.
- Trust surface per mode: web shows "nothing is stored — anywhere";
  installed shows "stored on this device only · wipe all".
- FactsTableEmitter wake condition unchanged: the storage schema is emitted
  from the F# verdict types.

## G7 · Backup & Restore (embraced — portability is an obligation of local-only)
- **Why it's in:** local-only storage without export = data trapped on a
  device — privacy's evil twin. Phone upgrades and laptop migrations are
  real; the user's verdicts are theirs to carry.
- **Format (versioned, boring, documented):**
  `gstflow-backup-YYYYMMDD.zip`
  ```
  manifest.json   — format version, engine version, rule-pack versions,
                    entry count, per-file SHA-256 checksums
  verdicts/*.json — the verdict envelopes (input hash, rules executed,
                    verdicts, timestamps) — the SAME envelope schema the
                    FactsTable emitter defines; one format, reused
  sources/        — OPTIONAL, default OFF: original PDFs/images. The user
                    explicitly opts in; verdicts without sources is the
                    privacy-preserving default
  ```
- **Restore:** import zip → verify checksums → dedupe by input hash →
  merge. Format-version mismatches get a clear message, never silent
  migration. Restore works across platforms (Windows backup → Android
  restore) because the envelope is platform-neutral JSON.
- Optional passphrase encryption of the zip: a checkbox, standard AES via
  the platform, no key management ceremony. Off by default.
- Fence: backup is a FILE the user carries (share sheet, USB, their own
  drive). GSTFlow never transmits it. The moment backup implies "to the
  cloud," see G6.3 — that is the server line, and the answer is no.

## G1-note · Modes without ceremony (confirmed)
No RBAC, no permissions, no lock icons. The mode toggle is a plain setting;
all three modes are equally free and equally available to everyone. The
Individual screen remains untouched by any of this — G3-revised and G7
live entirely behind installed-app + Facilitator/CA surfaces.

---

# PART H — UI/UX INSPIRATION BOARD (Material 3 Expressive + Indian patterns)

> Sources: Material 3 Expressive (May 2025, Google's most-researched design
> update — key finding: users find key elements up to 4× faster in
> expressive layouts) + the UPI-app patterns that trained India's muscle
> memory. Filtered adopt/adapt/reject against the constitution (C1–C6).

## H1 · ADOPT (M3 Expressive, serves the constitution)
- **Expressive type hierarchy:** the verdict word (PASS / FIX BEFORE FILING)
  is the largest, heaviest element on screen — M3E headline scale. C2 made
  visually literal.
- **Shape-morphing LoadingIndicator** for the <2s validation wait.
- **SplitButton** on verdict cards: primary = Share on WhatsApp; trailing =
  PDF / print / call supplier. G4's action loop in one component.
- **Spring motion + haptic thump on verdict arrival** — the verdict is felt,
  not just seen.
- **Shape library:** the rupee-checkmark motif as a morphing container —
  circle while checking → check-squircle on PASS. Brand = function.

## H2 · ADAPT (Indian patterns — weighted above Mountain View's)
- **UPI scan-first home:** camera affordance exactly where PhonePe/GPay
  muscle memory expects it. Inherit the placement; never innovate on it.
- **The soundbox sibling:** G2a voice verdicts are the invoice version of
  the shop soundbox ("received ₹500" aloud). Position it as such — trust-
  through-audio shopkeepers already live by, not an accessibility bolt-on.
- **First-launch language picker:** big tappable script names before
  anything else (ShareChat/Paytm pattern). Verbatim.
- **Full-screen green PASS:** a pass should feel like a payment received —
  unmissable, full, brief.

## H3 · REJECT (recorded so they stay out)
- **Dynamic color / wallpaper theming:** verdict colors are safety-critical
  brand constants; a red that isn't signal-red is a defect. Pin the palette;
  opt out of Material You theming deliberately.
- **Bottom nav bars / nav rails / floating toolbars:** one screen navigates
  nowhere. (M3E's own reviewers note component-swaps without purpose read
  as "Material 3.5" — chrome, not clarity.)
- **AI-contextual theme shifting:** trust products do not change appearance
  by usage. Ever.

## H4 · Implementation note
Tauri webview UI ≠ Jetpack Compose — adopt M3E as a DESIGN LANGUAGE
(type scale, motion curves, shapes, spacing) implemented in the web layer,
not as the Compose library. Spring curves via CSS/JS spring physics; the
components above are patterns to reproduce, not dependencies to import.
Zero new heavy deps — the constitution's Part A.8 stands.

---

# PART G — AMENDMENT 2: Cross-device resume (the local-first answer)

## G8 · Resume on another device = file handoff, not sync
- **Premise (steward's, correct):** modern devices out-compute Apollo;
  local-first is the architecturally modern stance, not a workaround.
  The server was a crutch for weak clients; that era ended.
- **Mechanism:** G7's backup zip IS the sync. New affordance only:
  **"Continue on another device"** — one tap = backup + share sheet.
  Transport = the user's own channel (WhatsApp self-chat — the national
  USB stick — USB, their own drive). GSTFlow transmits nothing; the
  pattern stays pure.
- **Receiving side:** opening a gstflow-backup zip offers "Resume" —
  restore + jump to the pending list. Input-hash dedupe makes repeated
  handoffs safe.
- **DORMANT (wake-conditioned):** LocalSend-style same-WiFi direct
  transfer (mDNS, device-to-device, genuinely serverless). Wakes only if
  real users report the share-sheet handoff insufficient.
- **REJECTED (the yell, on the record):** automatic/continuous/background
  sync in any form — live merge, conflict resolution, CRDTs, "both devices
  editing." That is the server's complexity smuggled in without the server:
  a distributed-systems project inside a bill checker. The workflow is
  sequential (one human, one device at a time); sequential needs a file,
  not a merge engine. If simultaneous multi-device editing is ever truly
  demanded, that is a different product, and this one will not become it.

---

# PART I — SOVEREIGN OFFLINE FEATURES (crypto + bundled data replace servers)

## I1 · IRN QR verification (offline anti-fraud — flagship sovereign feature)
Every e-invoice carries an IRP-SIGNED QR (JWS: IRN, GSTINs, amounts).
GSTFlow scans it and verifies the government's signature ON-DEVICE (IRP
public key ships in the rule pack). "✓ genuine registered e-invoice /
✗ forged QR" — fully offline, no API, no GSP-wall contact (reading a
signature, not calling a system). Catches fake-invoice input-credit fraud
at the counter. The signature travels with the paper; verification never
needed a network, only the key.

## I2 · Signed rule packs as files (the offline lifeline)
Rules change; offline devices must stay current WITHOUT a server:
- Rule packs = versioned, Ed25519-signed data files (rules, templates,
  HSN data, IRP public keys), imported like backups.
- Distribution = human channels: a CA downloads once, forwards to fifty
  clients on WhatsApp; each device verifies the signature before accepting.
  Tampered/unsigned pack → rejected, loudly.
- Staleness honesty: verdicts display "rules per pack v2026.07"; the app
  never pretends currency it can't verify.

## I3 · Duplicate/double-billing detection (local, from existing hashes)
Same supplier + same invoice number + different amount/date across the
workspace → flagged. The double-billing catch CAs actually want; zero new
architecture (the input-hash registry already exists).

## I4 · Bundled HSN directory (a few MB, static, public)
Offline HSN→rate lookup upgrades verdicts from "16% isn't a rate" to
"this HSN is 5%; the invoice charges 12%." Ships inside rule packs (I2)
so it updates through the same signed channel.

## I5 · Reverse-GST calculator (fenced)
Inclusive/exclusive breakup math shopkeepers do daily. Ten lines of
arithmetic, high daily utility — lives behind a small tools icon, NEVER
on the home surface (C1 stands). Utility, not engagement bait.

## I6 · DORMANT: tamper-evident verdict hash-chain
Court-grade chained history is cheap but is proof-theater for v1.
Wake condition: a CA requests evidentiary export by name.

---

# PART J — MEASURABLE GUARANTEES (GPT review intake; guarantees, not features)

## J1 · Product Constitution (adopted verbatim, sits above all parts)
GSTFlow exists for one purpose: **help honest businesses avoid preventable
GST mistakes before filing.** Everything else is secondary.
NEVER: accounting software · ERP · filing software · CRM · requires a
server · collects user data. (Binding on all future contributors, human/AI.)

## J2 · The Replay Law (elevated to constitutional; costs one CI test)
The verdict envelope (input hash · rule-pack version · engine version ·
verdicts) already enables it — now it is DECLARED and TESTED:
`replay(input, rulePack, engineVersion) = original verdict` — exactly,
years later, on any device. CI keeps N historical envelopes and replays
them every build. Any dispute is reproducible; any regression that changes
a historical verdict is caught before release.

## J3 · Verdict levels (engine speaks five; UI renders three)
Engine: PASS · PASS-WITH-ASSUMPTIONS · WARNING · UNKNOWN · NOT-SUPPORTED.
UNKNOWN is mandatory honesty — never pretend certainty the rules can't give.
Individual UI: green / amber / red, finer grade on tap. CA mode: all five.

## J4 · Rule metadata (capability matrix + provenance chain + evolution)
Every rule declares: RuleId · Category · Offline(always YES) · Confidence
(Exact/Approximate) · Languages covered · full provenance chain
(Section → Notification → Effective date → Rule pack → Engine version).
Rule-pack files gain: compatibility ("requires GSTFlow ≥ x.y") + migration
notes + (dormant until first use) deprecation metadata
(Deprecated → Replacement → Reason).

## J5 · The Unsupported Matrix (public, in README and in-app)
| Capability | Status |
|---|---|
| GSTIN structure + checksum | YES |
| Tax-split law, rate slabs, HSN format | YES |
| Reverse charge | NOT YET |
| Multi-currency / SEZ / exports | NO (v1) |
| E-way bill | NO |
| GSTN filing | NEVER (constitution) |
Users trust software that admits limits; the matrix ships with every release.

## J6 · Privacy Receipt (C3 extended into a shareable artifact)
After every validation, a receipt view: "Network calls: 0 · Bytes uploaded:
0 · Stored: No (web) / This device only (installed)". Screenshot-shaped on
purpose — the trust triad as evidence people forward.

## J7 · Release & build discipline
- Deterministic build manifest: git commit · compiler versions · rule-pack
  hash · template-pack hash · artifact hash — published per release.
- Release notes discipline: Changed rules · New rules · Removed rules ·
  Known limitations — every release, reviewable by the community.

## J8 · Standing statements (adopted verbatim)
- **AI is optional:** GSTFlow never requires AI. AI explanations may exist
  someday; validation never depends on AI.
- **Accessibility:** screen readers · high contrast · large fonts · voice
  output · keyboard navigation — first-class, tested.
- **CanonFlow dependency restriction:** GSTFlow may depend only on Canon IR,
  Verification, Facts Emitter (future), Agent Context (future). Nothing else.
- **Wording change (accepted):** "Build like it ships tomorrow" →
  **"Engineer every component to production quality; public release
  remains gated."**

## J9 · Refused from the review (recorded)
- **Numeric OCR confidence percentages ("Supplier 63%")** — false precision:
  the heuristic extractor measures nothing that justifies two significant
  figures. The certain/probable/guessed tiers state exactly what is known.
  Percentages are the claims-outrunning-mechanism pattern in miniature.

---

# PART K — THE SAW (v1 scope cut + reconciliation; GOVERNS ALL PARTS ABOVE)

> Precedence: on any conflict, K > J > earlier parts. Everything above
> remains the full spec; THIS part defines what NOW means. An executor
> builds K.1 and nothing else until K.1's exit test passes.

## K.1 · v1 — THE FALSIFIER BUILD (the only current scope)
Ships: **PWA (web) + NativeAOT CLI.** Nothing else.
- Individual mode ONLY. One screen: paste JSON / upload PDF / camera →
  confirmation screen (C5) → verdict.
- Verdict cards: 3-color UI, five engine levels underneath (J3), layman
  templates **EN + HI only** (T0). Tap for provenance.
- WhatsApp share card (C4) + tap-to-call supplier (one `tel:` line).
- Voice OUTPUT (G2a) — the soundbox button.
- Privacy receipt (J6) + offline badge (C3).
- Rules BAKED INTO the build (no pack mechanism yet); rule metadata (J4)
  present in code from day one.
- CI: specimens asserting verdicts (no `|| true`), tri-mode agreement
  (web/CLI), Replay Law (J2), template-completeness gate (EN+HI).
- Unsupported Matrix (J5) + Product Constitution (J1) in README and app.
**Exit test (the falsifier):** one real business validates one real invoice
correctly, witnessed; one CA reads the rule list. Until this passes,
NOTHING in K.2 begins. This is the whole point of the saw.

## K.2 · v1.1 — FIRST FAST-FOLLOW (starts only after K.1 exit test)
- **IRN QR offline verification (I1)** — the killer feature, first in line.
- Tauri Windows desktop + installed-app workspace (G3-revised):
  pending/verified, duplicate detection (I3), backup/restore +
  continue-on-device (G7/G8), print + zip.
- Facilitator & CA modes (G1).
- **Signed rule packs (I2)** — triggered at the FIRST real rule change —
  with HSN directory (I4) riding inside.
- T1 languages: Marathi, Gujarati, Tamil (F1).

## K.3 · v1.2 AND BEYOND (wake-conditioned; do not schedule)
Android APK · macOS/Linux · reverse-GST calculator (I5) · voice input
(G2b) · T2/T3 languages · SEVA.md community playbook · everything DORMANT
(I6 hash-chain, G8 LocalSend, J4 deprecation metadata) · iOS native.

## K.4 · Reconciliations (contradictions resolved)
1. Part E's "Build like it ships tomorrow" is SUPERSEDED by J8's wording:
   "Engineer every component to production quality; public release remains
   gated."
2. Part B's Tamil tagline is retained as brand asset; Tamil UI arrives in
   K.2 (F1/T1), not v1. No contradiction: the tagline ships when its
   language pack does.
3. Part D's platform matrix remains the map; K.1 activates only its first
   and last rows (PWA, CLI).
4. Launch gate (Part E, five locks) is UNCHANGED and applies to K.1's
   public release; the K.1 exit test (falsifier + CA read) covers locks
   1–2; locks 3–5 (CI history, legal session, CF stranger-demo) remain.

## K.5 · Integrity check for the executor
Before writing code, verify you can answer: What is v1? (K.1, verbatim.)
What language count? (Two.) What platforms? (PWA + CLI.) What modes? (One.)
What storage? (None — web is stateless; workspace is K.2.) When does
anything else begin? (After a real business and a real CA. Not before.)
If any answer differs, re-read K. The spec above is the country;
K is the road.

## K.3-addendum · V5 HORIZON (FYI, steward-filed, NOT scheduled)
On-device LLMs (Gemma 3n E2B/E4B via Google AI Edge, Qwen, et al.) now run
on 12GB-RAM Android flagships — the ONLY form of AI compatible with this
product's constitution (no server, no data leaving device). If ever woken
(v5, not before): extraction assist for messy layouts · conversational
vernacular explanation of verdicts · voice-input NLU — always upstream/
downstream of the deterministic engine, NEVER in the verdict path (J8).
Enters facilitator/CA tier first (flagship hardware lives there). Wake
condition: v1–v2 shipped, real users, and a documented case where the
deterministic pipeline demonstrably falls short. Filed by the steward
himself, who spotted it and holstered it in the same sentence.

## K.3-addendum-2 · V5 UNINVITED GUESTS (filed with the horizon; read before waking it)
Every local-LLM side effect is nondeterminism entering a determinism-branded
product: (1) plausible-wrong garnish on true verdicts — the brand pays;
(2) prompt injection via hostile invoice PDFs (Vishakanya, reincarnated);
(3) Replay Law breaks for explanations — must be scoped engine-only, declared;
(4) the open mic → advice liability; (5) vernacular quality degrades worst
for low-resource languages — the mission inverted; (6) QA shifts from total
template proofs to sampling; (7) feature magnetism toward chat-app gravity;
(8) 2–4GB thermal/RAM tax on mid-range mission phones; (9) "talks like a CA"
erodes the advisory line. Minimum fences if ever woken: LLM output visually
QUARANTINED (separate surface, labeled "AI explanation — not the verdict"),
injection-hardened extraction, replay scoped and declared, template layer
remains the only vernacular of record.

## K.3-addendum-3 · DORMANT: Windows-native deep integration (.NET/WinUI)
Filed after rebuke #1 (accepted by steward). The genuine kernel: first-class
Windows experience for CA offices — ALREADY served by K.2's Tauri signed exe
(same engine, no second UI). This dormant covers only the deeper form:
WinUI/WinML integration a webview cannot reach (e.g., on-device model
acceleration via Windows ML, shell/Explorer integration).
- Wake condition (ALL required): K.2 Tauri Windows shipped AND real CA users
  report a documented capability gap the webview cannot close AND .NET 11 is
  RTM (not preview) AND the on-device-AI wake (K.3-addendum) has itself fired.
- Identity law fence (binding): the engine and all logic remain F#. If a XAML
  shell exists, it contains ZERO logic — a thin host, nothing more. Any C#
  pattern beyond declarative shell markup gets the ⚠️ per the steward's own
  standing law. F# builds WinUI apps; prefer that path first.
- One-Engine Law unaffected: same Fable/AOT core, same verdicts, agreement
  suite extended to any new host before it ships.
