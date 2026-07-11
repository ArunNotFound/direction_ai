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
✓ Normal Tax Invoice
✓ Interstate B2B
✓ Intrastate B2B
✓ SEZ with LUT/Bond
✓ SEZ with IGST
✓ Export with LUT
✓ Export with IGST
✓ Deemed Export
✓ Reverse Charge (RCM)
✓ Bill-to / Ship-to
✓ Mixed GST Rates
✓ Nil-rated Items
✓ Exempt Items
✓ Non-GST Items
✓ Zero-rated Supply
✓ Composite Supply
✓ Mixed Supply
✓ E-invoice applicable
✓ E-invoice not applicable
✓ Invoice with Cess
✓ Invoice without Cess
✓ Foreign Currency Invoice
```

---

# 2. B2C (Business to Consumer)

```text
✓ B2C Small
✓ B2C Large
✓ Interstate
✓ Intrastate
✓ With Place of Supply
✓ Without Place of Supply
✓ Nil-rated
✓ Exempt
✓ Non-GST
✓ Mixed Rates
✓ Cess
✓ Digital Goods
✓ Services
✓ Goods
```

---

# 3. Credit Notes (CRN)

```text
✓ B2B Credit Note
✓ B2C Credit Note
✓ Partial Credit
✓ Full Credit
✓ Linked Invoice
✓ Missing Original Invoice
✓ Tax Reduction
✓ Quantity Reduction
```

---

# 4. Debit Notes (DBN)

```text
✓ B2B Debit Note
✓ B2C Debit Note
✓ Linked Invoice
✓ Missing Reference
✓ Tax Increase
✓ Price Increase
```

---

# 5. Special GST Supplies

```text
✓ Export
✓ Import
✓ SEZ Supply
✓ SEZ Receipt
✓ Deemed Export
✓ Reverse Charge
✓ Job Work
✓ Branch Transfer
✓ Stock Transfer
✓ High Sea Sale
✓ Merchant Export
```

---

# 6. Tax Scenarios

```text
✓ IGST
✓ CGST + SGST
✓ UTGST
✓ Compensation Cess
✓ Zero Tax
✓ Mixed Tax Rates
✓ Wrong Tax Split
✓ Wrong Tax Rate
✓ Missing Tax
✓ Excess Tax
```

---

# 7. GST Registration Scenarios

```text
✓ Registered Buyer
✓ Unregistered Buyer
✓ Composition Dealer
✓ Casual Taxpayer
✓ Non-resident Taxpayer
✓ Government Department
✓ Embassy
```

---

# 8. Invoice Lifecycle

```text
✓ Draft
✓ Issued
✓ Cancelled
✓ Amended
✓ Revised
✓ Duplicate
✓ Missing Number
✓ Out-of-sequence Number
```

---

# 9. Validation Edge Cases

```text
✓ Invalid GSTIN
✓ Invalid HSN
✓ Invalid SAC
✓ Invalid State Code
✓ Wrong Place of Supply
✓ Wrong Invoice Date
✓ Future Date
✓ Invalid Financial Year
✓ Negative Amount
✓ Zero Amount
✓ Decimal Rounding
✓ Duplicate Invoice
✓ Missing Mandatory Field
```

---

# 10. Return Classification

These determine where an invoice would typically be reported.

```text
✓ B2B
✓ B2CL
✓ B2CS
✓ Export
✓ SEZ
✓ Deemed Export
✓ Credit Note
✓ Debit Note
✓ Nil Rated
✓ Exempt
✓ Non-GST
✓ HSN Summary
```

---

# 11. Document Types

```text
✓ Tax Invoice
✓ Credit Note
✓ Debit Note
✓ Revised Invoice
✓ Bill of Supply
✓ Receipt Voucher
✓ Refund Voucher
✓ Payment Voucher
✓ Delivery Challan
```


