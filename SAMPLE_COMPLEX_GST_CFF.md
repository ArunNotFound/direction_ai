# Sample `.cff` Compliance Bundle: Complex Multi-Item GST Transaction
**Illustration of an Apache Avro / ZIP Compliance Package (`complex_b2b_rcm_sample.cff`)**

---

## What is Inside `complex_b2b_rcm_sample.cff`?

A `.cff` (CanonFlow Format) file is a deterministic, tamper-evident ZIP archive. Below is the exact illustration of its internal files for a complex **Interstate B2B Invoice** containing both **Regular Taxable Supplies** and **Section 9(3) Reverse Charge Mechanism (RCM)** services.

```
complex_b2b_rcm_sample.cff (ZIP Archive)
├── manifest.json         # Cryptographic seal & SHA-256 digests
├── invoices.json         # Human-readable / Avro logical decimal representation
├── verdicts.json         # F# Discriminated Union (DU) statutory outcomes
└── /attachments/
    └── INV-2026-8842_QR.raw  # Scanned e-Invoice cryptographic QR payload
```

---

## File 1: `manifest.json` (The Cryptographic Seal)

Every `.cff` bundle starts with a canonical manifest sealing the payload and audit results:

```json
{
  "cff_version": "2.0.0",
  "engine_id": "GSTFlow.Rules-NativeAOT-v2.4.0",
  "created_at": "2026-07-13T17:10:00Z",
  "rule_pack_hash": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855",
  "payload_digest": "8f434346648f6b96df89dda901c5176b10a6d83961dd3c1ac88b59b2dc327aa4",
  "files": [
    {
      "name": "invoices.json",
      "sha256": "8f434346648f6b96df89dda901c5176b10a6d83961dd3c1ac88b59b2dc327aa4"
    },
    {
      "name": "verdicts.json",
      "sha256": "4a1d48c8b6d8b9e6f2122c34a9b1c7845f29d38c1198f12d8a43516584c20d1e"
    }
  ]
}
```

---

## File 2: `invoices.json` (`invoices.avro` 128-bit Decimal Schema Representation)

Notice how monetary fields are represented with exact decimal strings (`scale = 4`) matching Avro's `logicalType: decimal(28,4)` to prevent any IEEE 754 floating-point drift:

```json
{
  "InvoiceNumber": "INV-2026-8842",
  "InvoiceDate": "2026-07-10",
  "SupplyType": "B2B",
  "DocumentType": "INV",
  "PlaceOfSupplyStateCode": "27",
  "IsInterstate": true,
  "Seller": {
    "Gstin": "29AAACR5055K1Z5",
    "LegalName": "AeroTech Dynamics Pvt Ltd",
    "StateCode": "29"
  },
  "Buyer": {
    "Gstin": "27AAACT8814B1Z2",
    "LegalName": "Western Logistics & Engineering Ltd",
    "StateCode": "27"
  },
  "LineItems": [
    {
      "LineNumber": 1,
      "HsnSacCode": "84713010",
      "Description": "High-Precision Industrial Server Blade",
      "IsReverseCharge": false,
      "Quantity": 2.0000,
      "UnitPrice": 125000.0000,
      "TaxableValue": "250000.0000",
      "GstRate": "18.0000",
      "CgstAmount": "0.0000",
      "SgstAmount": "0.0000",
      "IgstAmount": "45000.0000",
      "TotalLineAmount": "295000.0000"
    },
    {
      "LineNumber": 2,
      "HsnSacCode": "998211",
      "Description": "Legal Arbitration Advisory Services (Sec 9(3) RCM)",
      "IsReverseCharge": true,
      "Quantity": 1.0000,
      "UnitPrice": 50000.0000,
      "TaxableValue": "50000.0000",
      "GstRate": "18.0000",
      "CgstAmount": "0.0000",
      "SgstAmount": "0.0000",
      "IgstAmount": "9000.0000",
      "TotalLineAmount": "50000.0000",
      "RcmNote": "Tax ₹9,000.00 payable directly by Buyer under Reverse Charge"
    }
  ],
  "Summary": {
    "TotalTaxableValue": "300000.0000",
    "TotalForwardIgst": "45000.0000",
    "TotalReverseChargeIgst": "9000.0000",
    "GrandTotalPayableToSeller": "345000.0000",
    "StatutoryRoundedTotal": "345000.00"
  }
}
```

---

## File 3: `verdicts.json` (F# Discriminated Union Outcomes)

This file contains the deterministic execution results produced by `GSTFlow.Rules`, preserving F# Discriminated Unions (`Pass | Warning | Fail | Unknown`):

```json
{
  "InvoiceNumber": "INV-2026-8842",
  "OverallOutcome": "Pass",
  "VerifiedAt": "2026-07-13T17:10:02Z",
  "EnginePrecision": "System.Decimal (128-bit exact)",
  "RuleEvaluations": [
    {
      "RuleId": "RULE_MATH_EXACTNESS",
      "Outcome": "Pass",
      "EvidenceKind": "Direct",
      "Message": "Line item arithmetic verified: Taxable Value × Rate == Forward IGST (₹250,000 × 18% = ₹45,000.00)."
    },
    {
      "RuleId": "RULE_POS_INTERSTATE_IGST",
      "Outcome": "Pass",
      "EvidenceKind": "Direct",
      "Message": "Seller State (29-Karnataka) != POS State (27-Maharashtra). Correctly charged IGST instead of CGST/SGST."
    },
    {
      "RuleId": "RULE_RCM_LIABILITY_SPLIT",
      "Outcome": "Pass",
      "EvidenceKind": "Derived",
      "Message": "Line 2 identified as Section 9(3) Reverse Charge service (SAC 998211). Forward invoice payable total correctly excludes ₹9,000.00 RCM tax."
    },
    {
      "RuleId": "RULE_SEC_170_ROUNDING",
      "Outcome": "Pass",
      "EvidenceKind": "Direct",
      "Message": "Section 170 compliance verified. Grand Total ₹345,000.00 is exact Rupee integer."
    }
  ]
}
```

---

## How DuckDB Ingests This `.cff` Bundle

When an auditor drops `complex_b2b_rcm_sample.cff` into GSTFlow Desktop, DuckDB executes:

```sql
-- Querying exact 128-bit decimal fields from the Avro/JSON container
SELECT 
    InvoiceNumber,
    LineItems[1].HsnSacCode AS Item1_HSN,
    Summary.TotalTaxableValue AS Taxable_INR,
    Summary.TotalForwardIgst AS Forward_IGST,
    Summary.TotalReverseChargeIgst AS RCM_IGST
FROM read_json('complex_b2b_rcm_sample.cff/invoices.json');
```
