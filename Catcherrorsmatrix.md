Below is a standalone website-ready Markdown document. It separates guidance for ordinary users, accountants, and implementers; adds 5W1H, real-world examples, a publishable capability matrix, and a concise SWOT.

What GSTFlow Can and Cannot Prove

«GSTFlow is a deterministic GST guardrail. It is not a replacement for an accountant, auditor, GST portal, or legal adviser.»

GSTFlow checks whether the facts supplied to it are structurally valid, mathematically consistent, and compatible with the rules implemented in its active rule pack.

It cannot automatically prove that the supplied facts are true.

A GSTFlow result must therefore answer three questions clearly:

1. What was checked?
2. What evidence was used?
3. What remains unknown?

---

1. GSTFlow in One Sentence

GSTFlow helps detect selected GST invoice errors before filing or submission, especially:

- arithmetic mistakes;
- malformed identifiers;
- missing required information;
- inconsistent tax splits;
- unsupported or suspicious combinations;
- differences between represented values.

It does not automatically determine:

- whether a product has the correct HSN or SAC;
- whether the place of supply entered by the user is factually correct;
- whether ITC is legally eligible;
- whether a supplier filed the invoice;
- whether a GSTIN is currently active;
- whether a transaction is fraudulent;
- whether the GST portal will accept every possible payload.

---

2. The 5W1H of GSTFlow

What is GSTFlow?

GSTFlow is a local-first GST validation engine.

It receives structured invoice or return-related facts and applies deterministic checks using:

- F# domain types;
- exact decimal arithmetic;
- versioned GST rules;
- canonical output;
- explainable verdicts;
- explicit limitations.

It should be treated as a structural and mathematical assurance layer.

---

Why does GSTFlow exist?

Many GST errors happen before filing because of:

- incorrect totals;
- wrong GSTIN typing;
- invalid tax splits;
- missing fields;
- inconsistent invoice data;
- unsupported document combinations;
- copy-and-paste mistakes;
- incorrect JSON structures.

These errors waste time, delay invoicing, cause API rejection, and create avoidable compliance risk.

GSTFlow attempts to catch such defects early and locally.

---

Who should use GSTFlow?

Basic users

- small business owners;
- invoice operators;
- clerks;
- freelancers;
- MSMEs;
- users who do not understand portal error codes.

Accounting users

- accountants;
- GST practitioners;
- tax teams;
- ERP operators;
- finance controllers;
- reconciliation teams.

Technical users

- ERP developers;
- invoice-software vendors;
- ASP/GSP integrators;
- compliance-engine developers;
- auditors reviewing machine-generated evidence.

---

When should GSTFlow be used?

GSTFlow is most useful:

- before submitting e-invoice data;
- before exporting invoice JSON;
- before preparing a return;
- after importing invoices from PDFs or spreadsheets;
- before claiming that invoice totals reconcile;
- before sending data to an ERP or API;
- while reviewing GSTR-2B and books data;
- when verifying that a correction did not break another rule.

---

Where does GSTFlow run?

The target architecture supports local execution through:

- browser;
- desktop;
- mobile;
- command line;
- server integration.

The preferred principle is:

«Sensitive documents should remain on the user’s device unless the user explicitly chooses an external service.»

---

How does GSTFlow work?

Invoice / JSON / PDF / Spreadsheet
                ↓
Extraction or manual entry
                ↓
Canonical GST representation
                ↓
Deterministic GST checks
                ↓
Evidence-backed verdict

GSTFlow must keep two processes separate:

Extraction ≠ Validation

An OCR or AI model may extract candidate facts from a PDF.

Only the deterministic engine produces the compliance verdict.

---

3. Three Levels of Assurance

GSTFlow should never present every successful result as simply Valid.

It should report the assurance level.

Level 1 — Structural assurance

Checks whether the represented document is correctly formed.

Examples:

- required field present;
- GSTIN has valid shape;
- state code has valid format;
- HSN/SAC has expected digit length;
- invoice number is not blank;
- JSON matches the supported schema.

A structural pass means:

«The document has the shape expected by the installed rule pack.»

It does not mean the facts are true.

---

Level 2 — Internal consistency

Checks whether supplied values agree with each other.

Examples:

- taxable value × GST rate matches tax amount;
- CGST and SGST are split correctly;
- IGST is not combined with CGST/SGST;
- invoice totals reconcile;
- a credit note contains an original-document reference.

An internal-consistency pass means:

«The supplied numbers and related fields do not contradict each other under the implemented rules.»

---

Level 3 — External or contextual assurance

Checks the document against data outside the invoice.

Examples:

- GSTIN is active;
- supplier name matches registration records;
- IRN exists;
- signed QR is authentic;
- invoice appears in GSTR-2B;
- supplier filed the transaction;
- vehicle details are valid;
- HSN code matches the actual product;
- ITC is eligible for the business purpose.

These checks require:

- government data;
- signed records;
- ledger information;
- business context;
- human judgement;
- professional review.

Current GSTFlow should report these as:

Not checked
Needs external evidence
Needs business context
Needs professional review

---

4. For Basic Users

What a GSTFlow “Pass” means

A pass means:

«GSTFlow did not find an error in the checks that were performed.»

It does not mean:

«The invoice is fully legal, accepted by the GST portal, eligible for ITC, and safe from all notices.»

Always read the Not Checked section.

---

Simple example 1 — Correct mathematics

Input

Taxable value: ₹10,000
GST rate: 18%
CGST: ₹900
SGST: ₹900
Total: ₹11,800

GSTFlow result

Arithmetic: Pass
Tax split: Pass
GSTIN structure: Pass
HSN classification: Not checked
Supplier status: Not checked
ITC eligibility: Not checked

GSTFlow can prove that the represented calculation is internally consistent.

It cannot prove that 18% is the correct rate for the actual product.

---

Simple example 2 — Wrong tax split

Input

Seller state: Karnataka
Place of supply: Karnataka
Taxable value: ₹10,000
IGST: ₹1,800
CGST: ₹0
SGST: ₹0

GSTFlow result

Tax split: Fail
Reason:
The supplied transaction is represented as intra-state,
but IGST was charged.

Expected:
CGST + SGST

This is a strong GSTFlow use case.

---

Simple example 3 — Valid-looking but wrong GSTIN

The clerk enters a GSTIN that:

- has the right number of characters;
- passes checksum;
- belongs to a different company.

GSTFlow may report:

GSTIN structure: Pass
GSTIN ownership: Not checked
GSTIN active status: Not checked

This is not a GSTFlow failure.

The GSTIN is structurally valid, but the business fact is wrong.

---

Simple example 4 — PDF extraction uncertainty

An invoice PDF shows:

Invoice total: ₹15,000

OCR extracts:

Invoice total: ₹10,000

GSTFlow should not immediately validate ₹10,000.

It should show:

Extraction status: Needs review
Confidence: Low
Source page: 1
Detected text: "15,000"
Candidate value: "10,000"
Compliance status: Not evaluated

The user must confirm the extracted value before validation.

---

5. For Accountants and GST Practitioners

What GSTFlow can support

GSTFlow can be useful for:

- invoice arithmetic review;
- tax-head consistency;
- document-shape validation;
- duplicate detection within a supplied dataset;
- credit/debit-note references;
- e-invoice preparation;
  -Books-versus-return reconciliation;
- rule-pack-based historical replay;
- structured review evidence.

---

What GSTFlow must not decide without context

GSTFlow should not produce a final legal verdict for:

- HSN/SAC classification;
- composite versus mixed supply;
- blocked ITC;
- business versus personal use;
- place-of-supply special cases;
- reverse-charge applicability;
- export or SEZ treatment;
- valuation disputes;
- eligibility under statutory exceptions.

For such cases, the correct result is:

NeedsBusinessContext

or:

NeedsProfessionalReview

not:

Pass

---

Accountant example 1 — Blocked ITC

Scenario

A company buys employee meals and receives a GST invoice.

The invoice has:

- valid GSTIN;
- correct arithmetic;
- correct tax amount;
- correct date.

GSTFlow should report

Invoice structure: Pass
Arithmetic: Pass
Supplier filing: Not checked
ITC eligibility: Needs business context
Section 17(5) review: Required

GSTFlow cannot decide ITC eligibility from invoice mathematics alone.

The accountant must evaluate:

- purpose of purchase;
- recipient category;
- statutory exceptions;
- whether inward supply is used for making an outward taxable supply of the same category;
- other applicable conditions.

---

Accountant example 2 — GSTR-2B mismatch

Books

Supplier invoice: INV-9001
Taxable value: ₹1,00,000
IGST: ₹18,000

GSTR-2B

No matching invoice

GSTFlow reconciliation result

Invoice arithmetic: Pass
Books presence: Pass
GSTR-2B presence: Fail
Outcome: ITC_Risk
Reason:
Invoice exists in books but is not present in supplied GSTR-2B data.

This requires a reconciliation flow, not single-invoice validation.

---

Accountant example 3 — HSN mismatch

Invoice line

Description: Laptop computer
HSN entered: 4820

The HSN has four digits, so format may pass.

GSTFlow should report:

HSN format: Pass
HSN existence: Not checked
Description-to-HSN match: Needs review
Suggested action:
Confirm classification using the effective tariff master.

A future AI assistant may suggest HSN "8471", but it must remain advisory.

---

Accountant example 4 — Place-of-supply ambiguity

Scenario

- supplier in Karnataka;
- recipient registered in Maharashtra;
- goods installed at a site in Telangana.

A simple seller-state versus buyer-state comparison is insufficient.

GSTFlow should report:

Tax-head calculation: Deferred
Reason:
Place of supply depends on transaction facts.
Required facts:
- movement termination;
- installation location;
- bill-to and ship-to details;
- transaction category.

---

6. Publishable Capability Matrix

Legend:

- Yes — implemented deterministic check.
- Partial — limited support; important facts may be absent.
- No — not currently supported.
- External — requires authoritative external evidence.
- Human — requires business or professional judgement.

Capability| Status| What GSTFlow can prove| What it cannot prove
Parse supported GSTFlow JSON| Yes| Input matches the internal model| Official portal acceptance
Complete NIC e-invoice schema| Partial/No| Selected represented fields| Every official mandatory and conditional rule
GSTIN format and checksum| Yes| Identifier is structurally valid| GSTIN exists, is active, or belongs to the party
GSTIN legal-name match| External| Nothing without registry data| Identity from checksum alone
Invoice number present| Yes| Field is not blank| Portal-specific numbering compliance
Invoice-date presence| Yes| Date value is supplied| Whether date is legally valid for every scenario
Tax arithmetic| Partial| Implemented formulas reconcile| Every valuation, discount, charge, and rounding rule
CGST/SGST versus IGST| Partial| Tax head matches supplied scenario| Supplied POS is factually and legally correct
HSN/SAC digit format| Yes| Code has expected length| Correct classification
HSN/SAC existence| External| Nothing without master data| Whether code is current or applicable
GST rate validity| Partial| Rate is supported by rule pack| Correct rate for every item and condition
Reverse charge| Human/Partial| Selected explicit facts may be checked| Legal applicability from broad HSN alone
Credit/debit-note reference| Partial| Reference fields are present| Referenced invoice exists and adjustment is correct
IRN string format| Partial| Value has expected structural form| IRN is authentic or registered
Signed QR verification| No/External| Nothing currently| Document authenticity
Blocked ITC| Human| Nothing without purchase context| Eligibility under Section 17(5)
GSTR-2B reconciliation| No/Planned| Nothing in single-invoice mode| Supplier filing and recipient ITC availability
Supplier return filing| External| Nothing offline without return data| Actual filing status
PDF extraction| Partial| Candidate values may be extracted| Extracted values are always correct
Handwritten invoice extraction| Partial/Low confidence| Possible candidate data| Reliable unattended interpretation
Vehicle-number format| No/Planned| Nothing currently| Vehicle exists or was used
Transport distance| External| Nothing without route evidence| Distance is genuine
Fraud detection| No| Internal consistency only| Intent or fabricated evidence
Complete filing readiness| No| Supported checks only| Acceptance by every GST system
Historical rule replay| Partial/Planned| Only where versioned packs exist| Correct result without effective-dated rules
Canonical hash proof| Required| Exact document/verdict identity after implementation| Proof while hashing is absent or stubbed

---

7. What GSTFlow Should Say in the UI

Unsafe wording

Invoice is valid.

GST compliant.

Ready to file.

Valid GSTIN.

These statements are too broad.

---

Safer wording

The invoice passed the 18 checks performed by GSTFlow.

The represented tax amounts are internally consistent.

GSTIN format and checksum are valid.
Registration status was not checked.

No supported structural error was found.
HSN classification and ITC eligibility remain unverified.

---

8. Recommended Verdict Model

GSTFlow should report multiple dimensions instead of one global pass.

{
  "structuralStatus": "Pass",
  "arithmeticStatus": "Pass",
  "taxHeadStatus": "Pass",
  "externalStatus": "NotChecked",
  "classificationStatus": "NeedsBusinessContext",
  "itcStatus": "NotAssessed",
  "filingReadiness": "NotAssessed"
}

Recommended statuses:

Pass
Fail
Warning
Unknown
NotChecked
NeedsBusinessContext
NeedsProfessionalReview
Unsupported

---

9. Real-World Error Examples

Example A — Arithmetic typo

Entered

Taxable value: ₹25,000
GST rate: 18%
IGST entered: ₹4,050

Correct

₹25,000 × 18% = ₹4,500

Result

Fail
Expected IGST: ₹4,500
Entered IGST: ₹4,050
Difference: ₹450

GSTFlow catches this strongly.

---

Example B — Wrong valid HSN

Entered

Description: Office laptop
HSN: 4820
GST rate: 18%

All numbers may reconcile.

GSTFlow should not say:

Pass: HSN correct

It should say:

HSN format: Pass
Semantic classification: Not checked

---

Example C — Wrong place of supply

Entered

Seller state: Karnataka
Place of supply: Tamil Nadu
Tax type: IGST

The engine may correctly confirm IGST based on supplied facts.

But if the actual supply occurred entirely in Karnataka, the input is false.

Result should include:

Tax split: Internally consistent
Place-of-supply factual accuracy: Not verified

---

Example D — Supplier has not filed

The invoice is perfectly formed and mathematically correct.

But it is absent from GSTR-2B.

Single-invoice GSTFlow:

Pass for supported invoice checks
GSTR-2B presence: Not checked

Reconciliation GSTFlow:

ITC_Risk
Invoice missing in supplied GSTR-2B dataset

---

Example E — Fake but consistent invoice

A person fabricates:

- invoice number;
- GSTIN;
- taxable value;
- taxes;
- totals.

All numbers agree.

GSTFlow may report internal consistency.

It cannot prove that:

- supply occurred;
- goods were delivered;
- payment occurred;
- parties are genuine;
- invoice was reported.

This is the limit of deterministic document validation.

---

10. Concise SWOT

Strengths

- deterministic and repeatable;
- exact arithmetic;
- explainable rules;
- offline operation;
- fast local feedback;
- suitable for CI and ERP integration;
- can preserve evidence and rule versions.

Weaknesses

- only as good as supplied facts;
- limited legal context;
- incomplete without official schemas and masters;
- cannot independently determine intent;
- may give false confidence if “Pass” is poorly worded.

Opportunities

- official e-invoice pre-validation;
- GSTR-2B reconciliation;
- signed GST master data;
- PDF-to-structured-data review;
- vernacular explanations;
- HSN advisory assistance;
- accountant review workflows.

Threats

- changing GST rules;
- inaccurate OCR or AI extraction;
- overclaiming legal certainty;
- stale rate and master data;
- incorrect reverse-charge heuristics;
- users treating structural success as filing approval.

---

11. Product Roadmap

Phase 1 — Safe structural guardrail

- correct arithmetic checks;
- GSTIN checksum;
- tax-head consistency;
- clear unsupported states;
- real SHA-256;
- explicit checked/not-checked output;
- no legal overclaims.

Phase 2 — Official e-invoice validation

- complete official schema;
- conditional fields;
- field lengths and regexes;
- portal error-code mapping;
- accepted/rejected fixture corpus;
- measured rejection coverage.

Phase 3 — External truth sources

- GSTIN registration status;
- legal-name match;
- IRN and signed QR verification;
- effective-dated master data;
- supplier-status evidence.

Phase 4 — Reconciliation

Books
+
GSTR-2B
+
IMS
=
Reconciliation verdict

Support:

- missing in 2B;
- missing in books;
- amount mismatch;
- GST mismatch;
- duplicate;
- amendment;
- period mismatch;
- pending review.

Phase 5 — Advisory intelligence

- HSN/SAC suggestions;
- item-description matching;
- ITC-risk questionnaires;
- place-of-supply guidance;
- confidence and alternatives;
- mandatory human confirmation.

AI output must remain advisory.

---

12. Measuring Real Coverage

GSTFlow should not claim that it catches a percentage of GST errors until a benchmark exists.

Required benchmark groups:

- real IRP rejection payloads;
- valid IRP payloads;
- arithmetic defects;
- GSTIN status mismatches;
- place-of-supply scenarios;
- HSN classification cases;
- blocked-ITC cases;
- GSTR-2B reconciliation cases;
- credit/debit-note cases.

Required metrics:

Metric| Meaning
Precision| How many reported errors are genuine
Recall| How many known errors are detected
False-pass rate| Defective cases incorrectly passed
False-fail rate| Valid cases incorrectly failed
Unknown rate| Cases correctly deferred
IRP error-code coverage| Supported rejection classes
Scenario coverage| Supported legal scenarios
Rule-version coverage| Effective periods supported

A publishable claim should look like:

«On benchmark version 1.2, GSTFlow detected 86% of the supported IRP rejection cases with 98% precision. The benchmark does not measure HSN correctness, ITC eligibility, fraud, or supplier filing.»

It should not say:

«GSTFlow catches 40% of GST mistakes.»

---

13. Website Summary

For ordinary users

«GSTFlow checks your GST invoice for selected formatting, identifier, tax-split, and arithmetic problems. A successful result means no error was found in the checks performed. It does not confirm that every business fact is true or that the GST portal will accept the document.»

For accountants

«GSTFlow provides deterministic structural and arithmetic assurance. HSN classification, ITC eligibility, special place-of-supply rules, reverse charge, supplier filing, and external registration facts require additional evidence or professional judgement.»

For developers

«GSTFlow is a versioned rule engine, not a government-data oracle. Every verdict should identify the rules applied, evidence used, unsupported scenarios, engine version, and rule-pack version.»

---

Final Principle

GSTFlow’s strongest promise is not:

«We know that the invoice is fully compliant.»

Its strongest promise is:

«We tell you exactly what was checked, what was proved, what evidence was used, and what remains unknown.»

That honesty is not a limitation.

It is the foundation of trust.
