note all files  are heavy from kaggle  for mock  data  invoice

all here data.zip  here /root/repos/github/gst_sampledata

registered_companies.csv  contains company related info, 
Dataset_Generator_for_DTDC.csv person details
India 1.txt,India 2.txt contains first name last name etc ie persons
create Pan number for sample cant get that
do sample other data 

create logic in fsharp to create a sample data for below use as realstic as it can using above 
details , create many sample as required for below and run though GSTFlow lens and 
see we handle all these whichever applicable 



For GSTFlow testing, you want a **comprehensive fixture matrix**, not just B2B and B2C. Cover every invoice/document type and major GST scenario.

# 1. B2B (Business to Business)

```text
✓ [SUPPORTED] Normal Tax Invoice
✓ [SUPPORTED] Interstate B2B
✓ [SUPPORTED] Intrastate B2B
✓ [UNSUPPORTED] SEZ with LUT/Bond
✓ [UNSUPPORTED] SEZ with IGST
✓ [UNSUPPORTED] Export with LUT
✓ [UNSUPPORTED] Export with IGST
✓ [UNSUPPORTED] Deemed Export
✓ [SUPPORTED] Reverse Charge (RCM)
✓ [UNSUPPORTED] Bill-to / Ship-to
✓ [SUPPORTED] Mixed GST Rates
✓ [SUPPORTED] Nil-rated Items
✓ [SUPPORTED] Exempt Items
✓ [UNSUPPORTED] Non-GST Items
✓ [SUPPORTED] Zero-rated Supply
✓ [UNSUPPORTED] Composite Supply
✓ [UNSUPPORTED] Mixed Supply
✓ [LIMITED] E-invoice applicable (Format only)
✓ [SUPPORTED] E-invoice not applicable
✓ [SUPPORTED] Invoice with Cess
✓ [SUPPORTED] Invoice without Cess
✓ [UNSUPPORTED] Foreign Currency Invoice
```

---

# 2. B2C (Business to Consumer)

```text
✓ [SUPPORTED] B2C Small
✓ [SUPPORTED] B2C Large
✓ [SUPPORTED] Interstate
✓ [SUPPORTED] Intrastate
✓ [SUPPORTED] With Place of Supply
✓ [SUPPORTED] Without Place of Supply (Flags POS warning)
✓ [SUPPORTED] Nil-rated
✓ [SUPPORTED] Exempt
✓ [UNSUPPORTED] Non-GST
✓ [SUPPORTED] Mixed Rates
✓ [SUPPORTED] Cess
✓ [UNSUPPORTED] Digital Goods (Specific POS logic missing)
✓ [SUPPORTED] Services
✓ [SUPPORTED] Goods
```

---

# 3. Credit Notes (CRN)

```text
✓ [LIMITED] B2B Credit Note (Only checks DOC_TYPE & Ref)
✓ [LIMITED] B2C Credit Note
✓ [UNSUPPORTED] Partial Credit
✓ [UNSUPPORTED] Full Credit
✓ [SUPPORTED] Linked Invoice (CDN_ORIGINAL_INV rule)
✓ [SUPPORTED] Missing Original Invoice
✓ [UNSUPPORTED] Tax Reduction logic
✓ [UNSUPPORTED] Quantity Reduction logic
```

---

# 4. Debit Notes (DBN)

```text
✓ [LIMITED] B2B Debit Note
✓ [LIMITED] B2C Debit Note
✓ [SUPPORTED] Linked Invoice
✓ [SUPPORTED] Missing Reference (CDN_ORIGINAL_INV rule)
✓ [UNSUPPORTED] Tax Increase logic
✓ [UNSUPPORTED] Price Increase logic
```

---

# 5. Special GST Supplies

```text
✓ [UNSUPPORTED] Export
✓ [UNSUPPORTED] Import
✓ [UNSUPPORTED] SEZ Supply
✓ [UNSUPPORTED] SEZ Receipt
✓ [UNSUPPORTED] Deemed Export
✓ [SUPPORTED] Reverse Charge
✓ [UNSUPPORTED] Job Work
✓ [UNSUPPORTED] Branch Transfer
✓ [UNSUPPORTED] Stock Transfer
✓ [UNSUPPORTED] High Sea Sale
✓ [UNSUPPORTED] Merchant Export
```

---

# 6. Tax Scenarios

```text
✓ [SUPPORTED] IGST
✓ [SUPPORTED] CGST + SGST
✓ [UNSUPPORTED] UTGST
✓ [SUPPORTED] Compensation Cess
✓ [SUPPORTED] Zero Tax
✓ [SUPPORTED] Mixed Tax Rates
✓ [SUPPORTED] Wrong Tax Split (TAX_AMOUNT rule)
✓ [SUPPORTED] Wrong Tax Rate (RATE_SLAB rule)
✓ [SUPPORTED] Missing Tax
✓ [SUPPORTED] Excess Tax
```

---

# 7. GST Registration Scenarios

```text
✓ [SUPPORTED] Registered Buyer
✓ [SUPPORTED] Unregistered Buyer
✓ [UNSUPPORTED] Composition Dealer
✓ [UNSUPPORTED] Casual Taxpayer
✓ [UNSUPPORTED] Non-resident Taxpayer
✓ [UNSUPPORTED] Government Department
✓ [UNSUPPORTED] Embassy
```

---

# 8. Invoice Lifecycle

```text
✓ [SUPPORTED] Draft (Treated as standard document)
✓ [SUPPORTED] Issued
✓ [UNSUPPORTED] Cancelled
✓ [UNSUPPORTED] Amended
✓ [UNSUPPORTED] Revised
✓ [UNSUPPORTED] Duplicate
✓ [UNSUPPORTED] Missing Number (Sequence tracking)
✓ [UNSUPPORTED] Out-of-sequence Number
```

---

# 9. Validation Edge Cases

```text
✓ [SUPPORTED] Invalid GSTIN
✓ [SUPPORTED] Invalid HSN
✓ [SUPPORTED] Invalid SAC
✓ [SUPPORTED] Invalid State Code
✓ [SUPPORTED] Wrong Place of Supply
✓ [SUPPORTED] Wrong Invoice Date
✓ [UNSUPPORTED] Future Date
✓ [UNSUPPORTED] Invalid Financial Year
✓ [SUPPORTED] Negative Amount
✓ [SUPPORTED] Zero Amount
✓ [SUPPORTED] Decimal Rounding (SEC_170_ROUNDING)
✓ [UNSUPPORTED] Duplicate Invoice
✓ [SUPPORTED] Missing Mandatory Field
```

---

# 10. Return Classification

These determine where an invoice would typically be reported.

```text
✓ [SUPPORTED] B2B
✓ [SUPPORTED] B2CL
✓ [SUPPORTED] B2CS
✓ [UNSUPPORTED] Export
✓ [UNSUPPORTED] SEZ
✓ [UNSUPPORTED] Deemed Export
✓ [SUPPORTED] Credit Note
✓ [SUPPORTED] Debit Note
✓ [SUPPORTED] Nil Rated
✓ [SUPPORTED] Exempt
✓ [UNSUPPORTED] Non-GST
✓ [UNSUPPORTED] HSN Summary
```

---

# 11. Document Types

```text
✓ [SUPPORTED] Tax Invoice
✓ [SUPPORTED] Credit Note
✓ [SUPPORTED] Debit Note
✓ [UNSUPPORTED] Revised Invoice
✓ [UNSUPPORTED] Bill of Supply
✓ [UNSUPPORTED] Receipt Voucher
✓ [UNSUPPORTED] Refund Voucher
✓ [UNSUPPORTED] Payment Voucher
✓ [UNSUPPORTED] Delivery Challan
```
