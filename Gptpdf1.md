Your instinct is correct:

> Do not make GSTFlow’s mathematical validator responsible for understanding arbitrary PDF layouts.



A PDF is a presentation format. Two GST invoices may contain the same business facts but place them in completely different tables, fonts, pages, or scanned images. GSTFlow should validate structured facts, not guess where those facts appear visually.

First clarification: is there a common GST JSON format?

There is not one universal JSON format for every GST document.

There are several important structured formats:

1. GST e-invoice JSON

Indian B2B e-invoicing uses the standardized INV-01 / IRN JSON schema. The official e-invoice developer portal publishes schemas, sample JSON payloads, validations, regular expressions, master lists, and error lists. The current portal identifies Generate IRN API version v1.03, with enhancements announced as recently as June 18, 2026. 

This is the best initial canonical format for GSTFlow because it already defines concepts such as:

Version
TranDtls
DocDtls
SellerDtls
BuyerDtls
DispDtls
ShipDtls
ItemList
ValDtls
PayDtls
RefDtls
EwbDtls

For documents that have already been registered through an Invoice Registration Portal, obtaining the signed e-invoice JSON is much safer than extracting the printed PDF.

2. GSTR-1 JSON

GSTR-1 has a different structure because it represents a return for a tax period, not one invoice. It contains groups such as B2B invoices, exports, credit/debit notes, B2C summaries, nil/exempt supplies and HSN summaries. The GST Portal supports preparation through online entry, its Returns Offline Tool, or third-party ASP/GSP software. 

So:

E-invoice JSON = one transactional invoice model
GSTR-1 JSON    = return-period reporting model
PDF invoice    = visual representation

They overlap, but they are not interchangeable.

Best GSTFlow architecture

Use a three-layer architecture:

PDF / image / Excel / JSON
          ↓
Probabilistic or deterministic extraction
          ↓
GSTFlow Canonical Invoice IR
          ↓
Deterministic mathematical validation
          ↓
VerdictEnvelope

The crucial law should be:

Extraction \neq Verification

And:

UncertainExtraction
\Rightarrow
NoComplianceClaim

The AI may propose facts. Only CanonFlow validates facts.


---

Recommended solution: Canonical GST Invoice IR

Do not directly force every PDF into the full official GST JSON schema immediately.

Create a smaller internal type:

type ExtractedField<'a> =
    {
        Value: 'a option
        Confidence: decimal option
        SourcePage: int option
        SourceRegion: BoundingBox option
        RawText: string option
        ExtractionMethod: ExtractionMethod
    }

type GstInvoiceDraft =
    {
        DocumentType: ExtractedField<DocumentType>
        InvoiceNumber: ExtractedField<string>
        InvoiceDate: ExtractedField<LocalDate>
        SellerGstin: ExtractedField<Gstin>
        BuyerGstin: ExtractedField<Gstin>
        PlaceOfSupply: ExtractedField<StateCode>
        Items: ExtractedItem list
        InvoiceTotals: ExtractedTotals
    }

Then separate it from the verified model:

type VerifiedGstInvoice =
    private
        {
            Document: DocumentDetails
            Seller: RegisteredParty
            Buyer: Buyer
            Items: NonEmptyList<InvoiceItem>
            Totals: InvoiceTotals
        }

The transition becomes:

GstInvoiceDraft
    → extraction review
    → parsing
    → structural validation
    → mathematical reconciliation
    → VerifiedGstInvoice

This obeys “parse, don’t validate” and prevents low-confidence AI output from silently becoming trusted domain data.


---

PDF intake strategy

Path A — Prefer embedded structured evidence

Before using AI, attempt these in order:

1. Detect whether the PDF contains an e-invoice QR code.


2. Extract machine-readable text from the PDF.


3. Detect embedded XML, JSON or attached files.


4. Search for IRN, signed QR payload or invoice identifiers.


5. Let the user supply the original e-invoice JSON.


6. Only then use visual AI extraction.



This creates an evidence ladder:

Signed official JSON          strongest
Signed QR / IRN data
Embedded PDF text
Template parser
Vision-model extraction
Manual entry                  requires review

Do not treat all extracted values equally.

Path B — Browser extraction for simple PDFs

JavaScript can parse many text-based PDFs. The difficulty is not that JavaScript cannot parse dynamic fields; the difficulty is that a PDF often stores characters by coordinates rather than representing a meaningful table or form.

A browser can still do:

PDF.js
  → pages
  → text spans and coordinates
  → regex / GSTIN detector
  → table reconstruction

This works well for:

digitally generated PDFs;

consistent templates;

selectable text;

clear single-page invoices.


It performs poorly on:

scans;

rotated pages;

complex merged cells;

repeated headers;

multilingual content;

handwritten additions;

low-resolution images;

invoices whose reading order differs from visual order.


Therefore, do not remove browser parsing. Use it as the cheap first pass.


---

Gemma 4 E2B/E4B desktop option

You meant Gemma 4 E2B or E4B. These are Google’s smaller multimodal Gemma 4 variants, designed for local and edge use; Gemma 4 supports text and image inputs. E2B is smaller and cheaper, while E4B should generally provide better extraction quality at higher memory and compute cost. 

This could be an excellent optional local extraction sidecar:

GSTFlow Web
    ↓ user selects PDF
Local GSTFlow Extractor
    ↓ render pages as images
Gemma 4 E4B
    ↓ schema-constrained draft JSON
GSTFlow validator
    ↓ deterministic verdict

Possible packaging:

GSTFlow.Desktop
GSTFlow.Extractor
GSTFlow.Core
GSTFlow.Fable
CanonFlow.Core

The extractor can run through:

Ollama;

llama.cpp;

LM Studio;

a dedicated native executable;

a local HTTP service bound only to localhost.


Gemma 4 E2B/E4B can run locally, but hardware requirements and extraction quality will differ. 

Do not call it E2B sandbox

There is also an unrelated company/product called E2B, which provides cloud sandboxes for AI agents. That would mean sending documents into cloud infrastructure unless self-hosted and carefully configured. 

For GST invoices, local-first processing is preferable because documents may contain:

GSTINs;

customer names;

addresses;

bank details;

product and pricing information;

commercially sensitive transactions.



---

Critical trust boundary

The local model must never output:

{
  "valid": true
}

Instead it should only output observations:

{
  "invoiceNumber": {
    "value": "INV-1042",
    "confidence": 0.96,
    "page": 1,
    "evidence": "Invoice No: INV-1042"
  },
  "sellerGstin": {
    "value": "29ABCDE1234F1Z5",
    "confidence": 0.91,
    "page": 1,
    "evidence": "GSTIN: 29ABCDE1234F1Z5"
  }
}

Then CanonFlow determines:

GSTIN syntax valid?
State code consistent?
Tax split consistent?
CGST/SGST versus IGST correct?
Line totals reconcile?
Taxable value reconciles?
Invoice total reconciles?
Mandatory fields present?
Rounding acceptable?

This preserves:

AI = WitnessProducer

CanonFlow = VerdictProducer


---

Add field-level provenance

Every extracted field should retain:

value
page
bounding box
raw text
confidence
extractor name
extractor version
prompt/schema version
document SHA-256

Example:

{
  "field": "buyerGstin",
  "value": "29AAACB1234C1Z7",
  "source": {
    "page": 1,
    "box": [421, 164, 578, 184],
    "rawText": "GSTIN: 29AAACB1234C1Z7"
  },
  "extraction": {
    "method": "gemma-4-e4b",
    "modelVersion": "...",
    "confidence": 0.94
  }
}

The UI can highlight the exact region when the user clicks a field.

That is much stronger than showing unexplained JSON.


---

Use confidence only for review routing

Model confidence is not mathematical truth.

Use it like this:

≥ 0.95    pre-fill, still validate
0.75–0.95 highlight for review
< 0.75    require manual confirmation
missing   report Unknown

Do not convert low confidence into a failed GST compliance verdict.

Distinguish:

Invalid value
Missing value
Unreadable value
Conflicting values
Unsupported document

These are very different outcomes.


---

Multi-strategy extraction

A robust extractor should combine several methods:

1. PDF metadata and attachments
2. QR-code decoding
3. Native text extraction
4. GST-specific regex detection
5. Template/layout parser
6. Table extraction
7. Vision model
8. Human correction

Then reconcile competing candidates:

type Candidate<'a> =
    {
        Value: 'a
        Source: EvidenceSource
        Confidence: decimal
    }

type FieldResolution<'a> =
    | Resolved of Candidate<'a>
    | Ambiguous of Candidate<'a> list
    | Missing

Example:

PDF text says invoice total = 11,800
Vision says invoice total = 17,800
Arithmetic says subtotal + tax = 11,800

Result:
Ambiguous extraction
Recommended candidate: 11,800
Reason: text and arithmetic agree

CanonFlow can perform this reconciliation deterministically after the candidates are generated.


---

Narrow the first PDF scope

Do not begin with “all GST PDFs.”

Start with:

Input:
  one Indian tax invoice PDF

Supported:
  digitally generated
  English
  one invoice per PDF
  up to 5 pages
  selectable text or clear scan

Output:
  GSTFlow canonical invoice draft

Initial fields:

Invoice number
Invoice date
Seller GSTIN
Buyer GSTIN
Place of supply
Supply type
Line description
HSN/SAC
Quantity
Unit price
Taxable value
GST rate
CGST
SGST
IGST
Cess
Invoice total
IRN
Acknowledgement number/date

Only after this is reliable should you add:

credit notes;

debit notes;

delivery challans;

export invoices;

reverse charge;

e-commerce operator cases;

multi-currency;

mixed goods and services;

handwritten invoices;

consolidated GSTR-1 extraction.



---

User experience for the hosted GSTFlow playground

The current hosted application is identified as the GSTFlow Playground. 

I would add four intake tabs:

Paste JSON
Upload e-Invoice JSON
Upload PDF
Enter manually

For PDF:

Step 1: Document fingerprint
Step 2: Extract locally
Step 3: Review uncertain fields
Step 4: Validate
Step 5: Download evidence envelope

Show two separate statuses:

Extraction status: Needs review
Compliance status: Not evaluated

After review:

Extraction status: Confirmed
Compliance status: Failed — totals inconsistent

Never combine them into one green/red badge.


---

Stronger product idea: GSTFlow Evidence Studio

Instead of presenting it only as a validator, make it:

> A local-first GST invoice evidence and validation workbench.



Capabilities:

PDF → structured draft
JSON → canonical invoice
QR → signed invoice facts
Compare PDF with official JSON
Detect mismatches
Validate GST rules
Generate audit evidence
Export corrected JSON

A particularly useful feature would be:

PDF versus IRP JSON reconciliation

Input:

Printed invoice PDF
+
Official signed e-invoice JSON

GSTFlow compares:

Invoice number
Date
GSTINs
Item values
Tax values
Totals
IRN
QR information

This is more deterministic and commercially useful than pure PDF extraction.


---

Important warning about model reliability

Even leading multimodal models remain unreliable when asked to extract very broad nested schemas from complex PDFs. A 2026 structured-extraction benchmark found performance deteriorated sharply as schema breadth increased, with no tested model producing valid output on its largest 369-field financial schema. 

Therefore:

PDF \xrightarrow{LLM} JSON

must not be treated as:

PDF \xrightarrow{Proof} Truth

The safe law is:

PDF
\xrightarrow{Extractor}
Draft
\xrightarrow{Human/Deterministic\ checks}
Accepted\ IR
\xrightarrow{CanonFlow}
Verdict

My recommended order

1. Add official e-invoice JSON upload and validation.


2. Add PDF text extraction in-browser.


3. Decode QR/IRN where present.


4. Build a canonical GstInvoiceDraft with evidence per field.


5. Add review UI.


6. Add optional local Gemma 4 E4B extraction.


7. Compare PDF against official JSON.


8. Create a gold dataset of diverse invoices.


9. Measure field-level extraction accuracy.


10. Only then expand toward GSTR-1 preparation.



The strongest architecture is browser-first, local-model optional, deterministic validation mandatory.
