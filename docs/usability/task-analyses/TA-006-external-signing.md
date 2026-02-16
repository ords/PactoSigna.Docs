# External Signing - User Journeys

## Overview

Signing Requests allow Organization Members to send documents to external parties (auditors, suppliers, consultants) for Part 11 compliant electronic signatures without requiring those parties to create PactoSigna accounts.

Available only in the QMS dashboard (authenticated view). External signers access a standalone unauthenticated signing page via a secure link.

---

## Journey 1: Create and Send a Signing Request

### Actors

- **QA Manager** (internal Member)

### Preconditions

- QA Manager has one or more PDF documents ready to send for signature
- QA Manager knows the signer(s) name and email

### Steps

1. **Navigate to Signing Requests** -- QA Manager clicks "Signing Requests" in the QMS sidebar.
2. **Click "New Signing Request"** -- Opens the creation form.
3. **Enter title** -- e.g., "Q1 2026 Audit Report" or "Quality Agreement - Supplier ABC"
4. **Enter description** (optional) -- Additional context for signers.
5. **Upload documents** -- One or more PDFs (max 25MB each). Each appears in a document list with a thumbnail.
6. **Add signers** -- For each signer, enter name and email. e.g.:
   - Name: "Dr. Sarah Mueller", Email: "s.mueller@notified-body.eu"
   - Name: "John Smith", Email: "j.smith@supplier-abc.com"
7. **Place fields on each document** -- QA Manager clicks a document to open the **Field Placement Editor**:
   - PDF renders page-by-page in an embedded viewer
   - Left sidebar shows a field palette: `[Signature]` `[Initials]` `[Date]`
   - Each field is assigned to a specific signer via dropdown
   - QA Manager drags fields from the palette onto specific locations on PDF pages
   - Fields are resizable and snap to grid for alignment
   - Fields display the assigned signer's name as a label (e.g., "Dr. Mueller - Signature")
   - QA Manager navigates pages with page controls, zooms in/out as needed
   - Repeat for each document that needs fields
8. **Set signature meaning** -- Select from predefined options or enter custom text:
   - "I acknowledge receipt of this audit report"
   - "I have reviewed the enclosed documents"
   - "I agree to the terms of this quality agreement"
   - Custom (free text)
9. **Set expiry date** -- Default 14 days, max 30 days. Adjust as needed.
10. **Click "Send"** -- System validates:
    - At least one document uploaded
    - At least one signer added
    - Every signer has at least one field assigned
    - Every field has a signer assigned
11. **Signing request created** -- Emails sent to each signer with secure signing links.

---

## Journey 2: External Signer Completes Signing Ceremony

### Actors

- **External Signer** (auditor, supplier, consultant -- no PactoSigna account)

### Preconditions

- A Signing Request has been sent with this signer's email
- Signer has received the invitation email

### Steps

1. **Receive email** -- "[Acme Medical] has sent you documents for signing via PactoSigna"
2. **Click secure link** -- Opens in browser. No login required.
3. **See landing header** -- "[Acme Medical] has requested your signature" with the request title and description.
4. **First document renders inline** -- Embedded PDF viewer shows the first document. The signer's assigned fields are highlighted with orange/yellow borders and labels:
   - "Sign here" for signature fields
   - "Initial here" for initials fields
   - Date fields show as "Date" (auto-populated)
5. **Fill signature field** (first time) -- Signer clicks a signature field. A modal appears:
   - **Draw tab**: Canvas for freehand signature drawing
   - **Type tab**: Text input that renders in a signature font
   - Signer draws or types, then clicks "Apply"
   - The signature image is captured and auto-applied to this field AND all remaining signature fields for this signer
6. **Fill initials field** (first time) -- Same pattern as signature but for 2-3 character initials. Captured once and reused for all initials fields.
7. **Date fields** -- Auto-populated with current date. Shown as read-only. Server-validated at submission.
8. **Completed fields turn green** -- Visual feedback with a checkmark overlay.
9. **Navigate between documents** -- If multiple documents: "Next Document" / "Previous Document" buttons. Progress indicator: "Document 1 of 3".
10. **All fields filled** -- A **summary step** appears:
    - "By signing, you attest: [meaning text]"
    - Email verification: system sends a 6-digit code to the signer's email
    - Input field for the verification code
    - Legal notice: "This electronic signature is legally binding per 21 CFR Part 11"
11. **Enter verification code** -- 10-minute TTL. "Resend code" link available.
12. **Click "Complete Signing"** -- All field values submitted atomically.
13. **Confirmation screen** -- "Thank you. Your signature has been recorded."
14. **Confirmation email** -- Signer receives an email confirming they signed.
15. **Audit trail updated** -- Complete record of the signing ceremony.

**Atomic ceremony**: If the signer closes the browser before clicking "Complete Signing", nothing is saved. They can reopen the link and start the ceremony fresh.

---

## Journey 3: View and Manage Signing Requests

### Actor

- **QA Manager** or **Admin** (internal Member)

### Steps

1. **Navigate to Signing Requests** -- Sidebar link in the QMS dashboard.
2. **View table** -- Table columns:
   - Title
   - Status (chip: Sent, Partially Signed, Completed, Expired, Cancelled)
   - Signers ("1 of 2 signed")
   - Created date
   - Expires date
3. **Filter by status** -- Dropdown to filter: All, Sent, Completed, Expired, Cancelled.
4. **Click a row** -- Opens detail view with:
   - Request metadata (title, description, meaning, created by, dates)
   - Documents section (file names, hashes, download links)
   - Signers table (name, email, status, signed-at timestamp)
   - Audit trail (chronological list of events)
5. **Actions available**:
   - **Remind**: Resend signing email to pending signers
   - **Cancel**: Cancel the signing request (invalidates all signing links)
   - **Download Signed PDF**: Download the final PDF with all signatures overlaid (only when status is "Completed")

---

## Journey 4: Cancel or Expire a Signing Request

### Actor

- **QA Manager** (internal Member)

### Scenario A: Manual Cancellation

1. QA Manager navigates to a signing request in "Sent" or "Partially Signed" status.
2. Clicks "Cancel" button.
3. Confirmation dialog: "This will invalidate all signing links. Signers who haven't signed will no longer be able to. Continue?"
4. Clicks "Confirm".
5. Status changes to "Cancelled". Signing links return "This signing request has been cancelled" to any signer who tries to access them.

### Scenario B: Automatic Expiry

1. Signing request passes its expiry date with not all signers having signed.
2. A scheduled function checks for expired requests and updates status to "Expired".
3. QA Manager sees the request as "Expired" on the Signing Requests page.
4. QA Manager can create a new signing request if still needed.

---

## Navigation

### QMS Sidebar (All Members)

```
Documents
Traceability
Risks
Releases
Signing Requests    <-- NEW
Training
Audit Log
Settings
```

Signing Requests is only available in the QMS dashboard (authenticated view). It does not appear on the landing page or any unauthenticated view.

### External Signing Page

The external signing page is a **standalone unauthenticated route** (e.g., `/sign/:token`). It does not show the QMS sidebar, navigation, or any authenticated UI. Minimal PactoSigna branding only.

---

## Empty States

### Signing Requests Page (No Requests Yet)

**Title**: No Signing Requests

**Description**: Send documents to external parties for electronic signatures. Use this for audit report acknowledgments, quality agreements, and other documents that need external signatures.

**Action**: [New Signing Request]

### External Signing Page (Invalid/Expired Link)

**Title**: This signing link is no longer valid

**Description**: The signing request may have expired or been cancelled. Please contact the sender for a new link.

### External Signing Page (Already Signed)

**Title**: You have already signed this request

**Description**: Your signature was recorded on [date]. If you need a copy, please contact the sender.

---

## Audit Log Integration

All Signing Request actions create audit log entries:

| Internal Value              | Display Label             |
| --------------------------- | ------------------------- |
| `signing_request.created`   | Signing Request Created   |
| `signing_request.sent`      | Signing Request Sent      |
| `signing_request.viewed`    | Signing Request Viewed    |
| `signing_request.signed`    | Signing Request Signed    |
| `signing_request.declined`  | Signing Request Declined  |
| `signing_request.cancelled` | Signing Request Cancelled |
| `signing_request.expired`   | Signing Request Expired   |
| `signing_request.reminded`  | Signing Reminder Sent     |
