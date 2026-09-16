# AI Invoice Processing Automation

An AI-powered invoice processing workflow built with n8n, Google Drive, Google Gemini, Google Sheets, Gmail, and JavaScript.

The goal of this project was to cut down on manual invoice entry by automatically extracting invoice details, checking them for errors, assigning risk levels, and notifying the right person without anyone having to open the PDF manually.

## Workflow Overview

1. An invoice PDF is uploaded to Google Drive.
2. The workflow downloads the file.
3. Text is extracted from the PDF.
4. The Information Extractor uses Google Gemini to extract structured invoice information.
5. A JavaScript node validates the invoice.
6. The workflow checks for duplicate invoice numbers.
7. A risk level is calculated based on the validation results.
8. The invoice details are saved to Google Sheets.
9. A Switch node routes the invoice based on its risk level.
10. Gmail sends a notification based on the risk level.

## Tools Used

- n8n
- Google Drive
- Google Gemini
- Google Sheets
- Gmail
- JavaScript
- PDF extraction

## Invoice Information Extracted

- Vendor Name
- Invoice Number
- Invoice Date
- Due Date
- Currency
- Subtotal
- Tax
- Total Amount
- PO Number
- Payment Details

## Risk Levels

### Low Risk

The invoice passes validation. The calculation is correct, required information is present, and no duplicate invoice number is found.

Result: Risk Level - Low, Validation - Passed, Duplicate - No, Approval Status - Pending

### Medium Risk

The invoice is missing information, such as a PO number or payment details.

Result: Risk Level - Medium, Validation - Needs Review, Duplicate - No

### High Risk - Calculation Error

A subtotal of ₦100,000 plus tax of ₦7,500 should total ₦107,500, but the invoice listed ₦120,000. The mismatch triggers a high-risk flag.

Result: Risk Level - High, Validation - Failed, Duplicate - No

### High Risk - Duplicate Invoice

The invoice number already exists in the Google Sheets log.

Result: Risk Level - High, Validation - Failed, Duplicate - Yes

## Google Sheets Records

Each processed invoice is logged with:

- Invoice ID
- Vendor Name
- Invoice Number
- Invoice Date
- Due Date
- Currency
- Subtotal
- Tax
- Total Amount
- PO Number
- Payment Details
- Risk Level
- Validation Status
- Duplicate
- Approval Status
- Notes
- Processed Date

## Email Notifications

Gmail sends a different notification depending on the outcome:

- A low-risk confirmation
- A review request for medium-risk invoices
- A warning notification for high-risk invoices

## Testing

I tested the workflow against four scenarios:

1. A clean invoice with correct calculations
2. An invoice with missing fields
3. An invoice with a calculation error
4. An invoice with a duplicate invoice number

The tests showed that the workflow routed each scenario as expected.

## Challenges

The build wasn't smooth throughout. At one point, Google Gemini started throwing "too many requests" errors, which caused the Information Extractor to stop working entirely. Since every node downstream depended on that extracted data, the whole workflow broke until the issue cleared.

Beyond that, the risk logic itself took a lot of back and forth. Missing-field detection, duplicate checking, and the total calculation check all needed retesting more than once because the output didn't always match what I expected on the first run. Debugging the Switch node routing and getting the Gmail messages to trigger correctly for each branch also took a few passes to get right.

## What I Learned

Building the workflow itself was really only half the job. The harder part was making sure every branch of the logic actually held up once real invoice data went through it, not just when things looked right on paper.

Working through the Gemini rate-limit issue also taught me to think more about failure points in a workflow, not just the happy path.

## Final Result

What started as a single PDF upload now ends with a validated record in Google Sheets and the right person notified automatically, with no manual entry in between.