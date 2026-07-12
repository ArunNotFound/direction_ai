Trinity Cleanup Checklist (Gemini)

Execute in order. Keep every commit small. No opportunistic refactoring.

P0 — Must Fix

1. GSTFlow package migration

- Replace the sibling "ProjectReference" to "../../CanonFlow/..."
- Use:
  <PackageReference Include="CanonFlow.Core" Version="0.1.0-alpha" />
  (or centralized version management)
- Verify GSTFlow builds from a clean clone without requiring a sibling CanonFlow checkout.

---

2. Correct the AAR

Replace claims such as:

- "Mathematically proven"
- "Perfectly centralized"
- "Total strategic success"
- "Complete"

With wording like:

- "Verification-core extraction completed."
- "Phase I complete."
- "Constitutional conformance continues."

Do not claim proof beyond the evidence.

---

3. Update "mathsoverhaul.md"

Move old findings into a Historical State section.

Add a Current State section describing:

- verification extraction complete
- EDIFlow package migration complete
- GSTFlow package migration pending (or complete if fixed)
- remaining constitutional work

Keep history, but don't describe today's state using yesterday's measurements.

---

4. Contact ≥ 1

Create constitutional proof:

proof/contact/
    CONTACT-001.md

Include:

- anonymous external tester
- document hash
- expected verdict
- actual verdict
- confirmation
- engine version
- package version

---

P1 — Important

5. README consistency

Ensure README claims exactly match implementation.

Remove contradictions like:

- "One Engine Law complete"
  while simultaneously listing TODOs required to achieve it.

---

6. Native/Fable agreement

Run identical test corpus through:

- Native
- Fable

Verify:

- identical canonical JSON
- identical SHA256
- identical verdict envelope

Document the proof.

---

7. Foundation site

If GitHub Pages exists:

- make repository public or
- document where it lives.

Ensure every README links to the canonical location.

---

8. Release manifest

Generate a machine-readable file:

{
  "canonflow":"commit/package",
  "gstflow":"commit",
  "ediflow":"commit",
  "tests":"pass",
  "agreement":"pass",
  "contact":"CONTACT-001"
}

Publish it with every release.

---

Final verification

Run:

- Clean clone build
- Native build
- Native AOT publish
- Fable build
- Unit tests
- Agreement tests
- Property tests
- Contact proof present
- README matches implementation
- No sibling repository assumptions

Only then declare the Trinity constitutionally aligned.
