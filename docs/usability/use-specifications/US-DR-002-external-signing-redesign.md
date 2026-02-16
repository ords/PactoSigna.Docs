# Redesign Proposal: Remove Auditor Role, Introduce Signing Requests

## Summary

This proposal merges and replaces two existing feature requests:

- **GitWeb#45**: "Redesign Auditor Features" (auditor role, read-only access, simplified views)
- **Sample.QMS.ISO13485#10**: "Contract Management" (upload docs, designate signature locations, external signing)

**Core thesis**: Auditors should NOT be members of the platform. Instead, PactoSigna should (1) keep export packages as the primary delivery mechanism for auditors and (2) introduce a "Signing Request" feature that allows external parties to sign documents without an account.

---

## What Changes

### Remove: Auditor as a Member Role

**Current state**: `MemberRole = 'admin' | 'member' | 'auditor'`
**Proposed state**: `MemberRole = 'admin' | 'member'`

The auditor role is removed from the member model entirely. No more auditor-specific permissions, views, or invitation flows. This simplifies:

- The permission model (2 roles instead of 3)
- The invitation flow (no expiry logic needed)
- The frontend (no role-based view switching)
- Security (no external users with system accounts)

### Keep: Export for Auditors

The existing export functionality (`POST /releases/:releaseId/export`) already produces comprehensive audit packages:

- Individual document PDFs (rendered from markdown at specific commit SHAs)
- Attestation PDF with release info, signatures, traceability matrix, risk matrix, gap analysis
- ZIP bundle with all documents
- Email delivery with signed download URL
- 30-day retention in Cloud Storage

This feature is already well-built and operates independently from Signing Requests.

### Add: Signing Request (New Bounded Context)

A new feature that replaces both the auditor signature flow AND the "contract management" proposal. Signing Requests allow internal members to send documents to external parties for Part 11 compliant electronic signatures.

**Key capability**: Senders can visually place signature, initials, and date fields at specific locations on uploaded PDFs. External signers see the PDF inline with highlighted fields and fill them in — similar to DocuSign's prepare-and-sign workflow.

---

## Signing Request: Detailed Design

### Domain Model

> **Bounded Context Separation**: SigningDocuments are value objects within the External Signing BC. They are NOT related to the Document Management BC's `Document` entity (which represents Git-synced markdown files with frontmatter IDs). No shared model, repository, or Firestore collection between the two.

```
SigningRequest (Aggregate Root)
├── Title
├── Description (optional)
├── Status: sent | partially_signed | completed | expired | cancelled
├── Meaning (signature attestation text)
├── Created by (Member)
├── Created at
├── Expires at (required, max 30 days from creation)
├── SigningDocuments[] (value objects — uploaded PDFs)
│   ├── File name
│   ├── Storage path (Cloud Storage)
│   ├── File hash (SHA-256)
│   ├── Page count
│   ├── Uploaded at
│   └── Fields[] (positioned on the PDF by the sender)
│       ├── Field ID (unique within request)
│       ├── Type: 'signature' | 'initials' | 'date'
│       ├── Assigned signer email
│       ├── Page (1-indexed)
│       ├── X (percentage of page width, 0-100)
│       ├── Y (percentage of page height, 0-100)
│       ├── Width (percentage)
│       └── Height (percentage)
├── Signers[] (external parties)
│   ├── Name
│   ├── Email
│   ├── Status: pending | viewed | signed | declined
│   └── Secure token (unique per signer)
└── Audit trail[]
    ├── Event type (created, sent, viewed, signed, declined, expired)
    ├── Actor (member or signer email)
    └── Timestamp
```

**Signature data** (captured when a signer completes the signing ceremony):

```
/signingRequests/{id}/signatures/{signatureId}
├── Signer email, signer name
├── Signed at (server timestamp)
├── IP address
├── Meaning (copied from SigningRequest)
├── Identity verification: { method: 'email_code', verifiedAt }
├── Signature image (storage ref — drawn or typed, captured once)
├── Initials image (storage ref — drawn or typed, captured once)
└── Field values[] (what was placed where)
    ├── Field ID (references SigningDocument field)
    ├── Type: 'signature' | 'initials' | 'date'
    └── Value (storage ref for images, ISO string for dates)
```

**Design decisions:**

- **Percentage-based coordinates**: Fields are positioned as percentages of page dimensions, not absolute pixels. This ensures correct placement regardless of viewer zoom or screen size.
- **Signature/initials captured once, referenced by fields**: The signer draws their signature once. Each field value references the same image. Avoids duplicating image data across many initial fields.
- **No draft status**: When the sender clicks "Send", the request is created and emails are sent immediately.
- **No sequential signing**: All signers can sign in parallel (v1).

### User Flows

#### Flow 1: Create Signing Request

**Actor**: Admin or Member

1. Member navigates to "Signing Requests" from the QMS sidebar
2. Clicks "New Signing Request"
3. Enters title and optional description
4. Uploads one or more PDF documents (max 25MB per file)
5. **Adds external signers** (name + email for each)
6. **Opens Field Placement Editor** for each document:
   - PDF renders page-by-page in an embedded viewer
   - Left sidebar has a field palette: `[Signature]` `[Initials]` `[Date]`
   - Each field can be assigned to a specific signer (dropdown)
   - Member drags fields from the palette onto PDF pages
   - Fields are resizable and snap to grid for alignment
   - Member can navigate pages and zoom
   - Fields display the assigned signer's name as a label
7. Selects signature meaning from predefined options or enters custom text:
   - "I acknowledge receipt of this audit report"
   - "I have reviewed the enclosed documents"
   - "I agree to the terms of this quality agreement"
   - Custom meaning (free text)
8. Sets expiry date (default: 14 days, max: 30 days)
9. Clicks "Send"
10. System creates the signing request and sends email to each signer

**Validation before send:**

- Every signer must have at least one field assigned
- Every field must have a signer assigned
- At least one signer is required
- At least one document is required

This flow supports: quality agreements, audit reports, supplier agreements, consultant deliverables, and any other document requiring external signatures.

#### Flow 2: External Signer Signs

**Actor**: External party (auditor, supplier, consultant)

1. Signer receives email: "[Organization] has sent you documents for signing via PactoSigna"
2. Clicks secure link in email
3. Browser opens a standalone signing page (no PactoSigna login required)
4. Signer sees a landing header: "[Organization] has requested your signature" with the request title
5. **First document renders as an embedded PDF** with the signer's assigned fields highlighted (orange/yellow borders with labels: "Sign here", "Initial here", "Date")
6. Signer clicks each highlighted field to fill it:
   - **Signature field**: Opens a modal with draw-on-canvas OR type name (rendered in signature font). Once captured, the signature auto-applies to all remaining signature fields
   - **Initials field**: Same pattern — draw or type 2-3 character initials. Captured once, reused for all initials fields
   - **Date field**: Auto-populated with current date (server-validated at submission), shown as read-only
7. Completed fields turn green with a checkmark
8. If multiple documents: "Next Document" button advances to the next PDF. Progress shown: "Document 1 of 3"
9. After all fields on all documents are filled, a **summary step** appears:
   - "By signing, you attest: [meaning text]"
   - Email verification prompt: system sends a 6-digit code to the signer's email
   - Legal notice: "This electronic signature is legally binding per 21 CFR Part 11"
10. Signer enters verification code and clicks "Complete Signing"
11. Confirmation screen shown. Signer receives a confirmation email
12. Audit trail entries created for the complete ceremony
13. If all signers have signed, status moves to "completed" and internal member is notified

**Signing ceremony is atomic**: No partial state is saved. If the signer closes the browser mid-signing, they reopen the link and start fresh. All field values are submitted together at the "Complete Signing" step.

#### Flow 3: Track and Manage Signing Requests

**Actor**: Admin or Member

1. Signing Requests page shows a table:
   - Title
   - Status (chip: Sent, Partially Signed, Completed, Expired, Cancelled)
   - Signers (count: "2 of 3 signed")
   - Created date
   - Expires date
2. Clicking a row shows:
   - Full signing request details
   - Per-signer status with timestamps
   - Document list with hashes
   - Complete audit trail
   - Actions: Remind (resend email), Cancel, Download signed PDF

#### Flow 4: Cancel or Expire a Signing Request

**Scenario A: Manual Cancellation**

1. Member navigates to a signing request in "Sent" status
2. Clicks "Cancel"
3. Confirmation dialog: "This will invalidate all signing links. Signers who haven't signed will no longer be able to. Continue?"
4. Status changes to "Cancelled". Signing links show: "This signing request has been cancelled"

**Scenario B: Automatic Expiry**

1. Signing request passes its expiry date without all signatures collected
2. A scheduled function updates status to "Expired"
3. Member sees the request as "Expired" and can create a new one if needed

---

## Technical Architecture

### New Dependencies

| Layer    | Library                  | Purpose                                                                    |
| -------- | ------------------------ | -------------------------------------------------------------------------- |
| Frontend | `@react-pdf-viewer/core` | Render PDFs in browser for field placement editor and signer view          |
| Frontend | `react-signature-canvas` | Capture drawn signatures and initials                                      |
| Backend  | `pdf-lib`                | Overlay signatures/initials/dates onto PDFs to produce final signed copies |

### Existing Infrastructure Reused

| Component                                   | How It's Reused                                 |
| ------------------------------------------- | ----------------------------------------------- |
| Cloud Storage (`@google-cloud/storage`)     | Store uploaded PDFs and signature images        |
| Audit log integration                       | All signing actions create audit entries        |
| Email sending (from export worker patterns) | Send signing invitation and verification emails |
| Service/Controller architecture             | Same Fastify module pattern                     |
| React Query ViewModel hooks                 | Same data fetching pattern                      |
| MUI component library                       | Same UI component system                        |

### What Is NOT Reused

The existing internal signature system (used for release signatures) is a **completely separate concern**:

| Aspect          | Internal Signatures                 | Signing Request Signatures                 |
| --------------- | ----------------------------------- | ------------------------------------------ |
| Signer          | Authenticated Member                | External party (no account)                |
| Identity        | Firebase Auth re-authentication     | Email verification code                    |
| Visual          | No visual signature (metadata only) | Drawn/typed signature + initials on PDF    |
| Placement       | Whole-document scoped               | Positioned at (x, y) on specific PDF pages |
| Storage         | Firestore only                      | Firestore + Cloud Storage (images, PDFs)   |
| Bounded context | Release Management                  | External Signing                           |

No code sharing between the two signature systems. They are different bounded contexts solving different problems.

---

## Impact on Existing Codebase

### Types to Change (Remove Auditor)

| File                                          | Change                                  |
| --------------------------------------------- | --------------------------------------- |
| `packages/shared/src/types/member.ts`         | Remove `'auditor'` from `MemberRole`    |
| `packages/shared/src/types/release.ts`        | Remove `'auditor'` from `SignatureType` |
| `packages/shared/src/schemas/index.ts`        | Remove `'auditor'` from Zod enum        |
| `packages/data/src/models/memberModels.ts`    | Remove `'auditor'` from role            |
| `packages/contracts/src/entities.ts`          | Remove `'auditor'` from role            |
| `packages/contracts/src/identity/requests.ts` | Remove `'auditor'` from `RoleSchema`    |

### Backend to Change

| File                                                            | Change                                           |
| --------------------------------------------------------------- | ------------------------------------------------ |
| `apps/services/src/modules/releases/ReleasesService.ts`         | Remove auditor-specific signature logic          |
| `docs/architecture/adr/adr-005-department-based-permissions.md` | Update permission matrix (remove Auditor column) |
| `docs/architecture/adr/adr-007-hybrid-signature-flow.md`        | Remove auditor signature type                    |

### Frontend to Change

| File                                                                | Change                                                   |
| ------------------------------------------------------------------- | -------------------------------------------------------- |
| `apps/web/src/features/signatures/components/SignaturePanel.tsx`    | Remove auditor label                                     |
| `apps/web/src/features/settings/pages/OrganizationSettingsPage.tsx` | Remove auditor from role dropdown                        |
| `apps/web/src/features/releases/pages/ReleaseDetailPage.tsx`        | Rename "Export for Auditors" to "Export Release Package" |
| `apps/functions/src/export/attestation.ts`                          | Remove auditor from signature type formatter             |

### New Code Required

| Component                       | Location                                                                                     |
| ------------------------------- | -------------------------------------------------------------------------------------------- |
| **SigningRequest types**        | `packages/shared/src/types/signing-request.ts`                                               |
| **SigningRequest contracts**    | `packages/contracts/src/signing-requests/`                                                   |
| **SigningRequest data model**   | `packages/data/src/models/signingRequestModels.ts`                                           |
| **SigningRequest repository**   | `packages/data/src/repositories/SigningRequestsRepository.ts`                                |
| **SigningRequest service**      | `apps/services/src/modules/signing-requests/SigningRequestsService.ts`                       |
| **SigningRequest controller**   | `apps/services/src/modules/signing-requests/SigningRequestsController.ts`                    |
| **Field Placement Editor**      | `apps/web/src/features/signing-requests/components/FieldPlacementEditor.tsx`                 |
| **Signing Requests list page**  | `apps/web/src/features/signing-requests/pages/SigningRequestsPage.tsx`                       |
| **Signing Request detail page** | `apps/web/src/features/signing-requests/pages/SigningRequestDetailPage.tsx`                  |
| **New Signing Request page**    | `apps/web/src/features/signing-requests/pages/NewSigningRequestPage.tsx`                     |
| **External signing page**       | `apps/web/src/features/signing-requests/pages/ExternalSigningPage.tsx` (standalone, no auth) |
| **Signature capture component** | `apps/web/src/features/signing-requests/components/SignatureCapture.tsx`                     |
| **Email notification worker**   | `apps/functions/src/signingNotificationWorker.ts`                                            |
| **Signed PDF generator**        | `apps/functions/src/signingPdfWorker.ts` (uses pdf-lib to overlay signatures)                |
| **Expiry checker function**     | `apps/functions/src/signingExpiryChecker.ts` (scheduled, checks for expired requests)        |

---

## Bounded Context: External Signing

This introduces a new bounded context to the domain model. It is **fully independent** — no cross-BC references to Document Management or Release Management.

### Aggregates

- **SigningRequest**: The root aggregate. Contains SigningDocuments (value objects), signers, fields, and audit trail.

### Key Behaviors

- Create signing request (with uploaded PDFs, placed fields, and designated signers)
- Send signing invitation emails
- Record external signer's signature (with Part 11 fields)
- Generate final signed PDF (overlay signatures/initials/dates onto original)
- Track signing progress
- Auto-expire after deadline
- Cancel a signing request
- Resend reminder emails

### Relationships

- **Identity & Access** (upstream): Only active Members can create signing requests
- **Audit** (shared kernel): All signing request actions generate audit log entries
- No relationship to Document Management or Release Management

### Firestore Structure

```
/organizations/{orgId}/signingRequests/{requestId}
  title, description, status, meaning, createdBy, createdAt, expiresAt
  signingDocuments: [{
    fileName, storagePath, fileHash, pageCount, uploadedAt,
    fields: [{ id, type, assignedSignerEmail, page, x, y, width, height }]
  }]
  signers: [{ name, email, status, token }]

/organizations/{orgId}/signingRequests/{requestId}/signatures/{signatureId}
  signerEmail, signerName
  signedAt, ipAddress, meaning
  identityVerification: { method: 'email_code', verifiedAt }
  signatureImage (storage ref)
  initialsImage (storage ref)
  fieldValues: [{ fieldId, type, value }]

/organizations/{orgId}/signingRequests/{requestId}/auditTrail/{entryId}
  eventType, actor, timestamp, details
```

---

## Part 11 Compliance for External Signing

Since external signers don't have platform accounts, this is an **open system** under 21 CFR Part 11. Additional controls required:

| Requirement               | Implementation                                                        |
| ------------------------- | --------------------------------------------------------------------- |
| **Identity verification** | Email verification via 6-digit code before signing                    |
| **Encryption in transit** | TLS 1.3 for all communication                                         |
| **Encryption at rest**    | Cloud Storage default encryption + customer-managed keys (future)     |
| **Signature uniqueness**  | Each signer has a unique token; email verified per session            |
| **Signature meaning**     | Pre-defined or custom meaning selected by request creator             |
| **Timestamp**             | Server-generated, not client-supplied                                 |
| **Audit trail**           | Complete record of: link sent, viewed, code verified, signed/declined |
| **Tamper evidence**       | Document hashes captured at upload; verified at signing time          |

---

## Error Handling & Edge Cases

### Sender-side

| Scenario                            | Handling                                                                                     |
| ----------------------------------- | -------------------------------------------------------------------------------------------- |
| PDF upload fails                    | Standard retry. Validate file type (PDF only) and size (25MB max) before upload              |
| PDF is corrupted/unparseable        | Viewer fails to render. Show: "This PDF could not be processed. Please try a different file" |
| No fields placed on a document      | Prevent sending. Validate every signer has at least one field                                |
| Signer added but no fields assigned | Warn: "[Name] has no fields assigned"                                                        |

### Signer-side

| Scenario                           | Handling                                                    |
| ---------------------------------- | ----------------------------------------------------------- |
| Link expired                       | Show: "This signing link is no longer valid"                |
| Link already used (already signed) | Show: "You have already signed this request" with timestamp |
| Email verification code expires    | 10-minute TTL. Allow "Resend code"                          |
| Browser doesn't support canvas     | Fall back to typed signature only                           |
| Signer closes browser mid-signing  | No partial state saved. Reopen link to start fresh          |

### System-side

| Scenario                    | Handling                                                                                               |
| --------------------------- | ------------------------------------------------------------------------------------------------------ |
| Signed PDF generation fails | Retry with backoff. Signature metadata in Firestore is source of truth; PDF is derived and regenerable |
| Email delivery fails        | Log failure. Sender can click "Remind" to resend                                                       |

---

## Explicitly Out of Scope (v1)

1. **No release export integration** — Signing Requests and Release Export are independent features
2. **No sequential signing order** — all signers sign in parallel
3. **No draft signing requests** — Send creates and sends immediately
4. **No template reuse** — can't save field placements as templates
5. **No PDF editing** — sender places fields on top of the PDF, cannot modify content
6. **No signer-side document upload** — signers can only sign, not attach documents
7. **No partial signing resume** — signer starts fresh if browser is closed
8. **No mobile-optimized signing** — responsive but not a dedicated mobile experience

---

## Migration Path

### Phase 1: Remove Auditor Role

- Remove auditor from type definitions and Zod schemas
- Migrate any existing auditor members to "member" role
- Update ADRs and domain documentation
- Rename "Export for Auditors" to "Export Release Package"

### Phase 2: Build Signing Requests

- Build the External Signing bounded context (types, contracts, data, service, controller)
- Build the Field Placement Editor (PDF viewer + drag-and-drop fields)
- Build the External Signing Page (PDF viewer + field filling + signature capture)
- Build the management pages (list, detail, create)
- Build email notification and verification workers
- Build signed PDF generation (pdf-lib overlay)
- Build expiry checker scheduled function

---

## Naming Decision

### "Signing Request" vs "Contract Management"

| Criterion                      | Signing Request                                    | Contract Management                            |
| ------------------------------ | -------------------------------------------------- | ---------------------------------------------- |
| ISO 13485 terminology conflict | None                                               | **Severe** -- Clause 4.1.5 and 7.4             |
| User clarity                   | Clear action: "I'm requesting someone's signature" | Ambiguous: "Am I managing supplier contracts?" |
| Developer familiarity          | Mirrors "Pull Request"                             | No equivalent                                  |
| Scope accuracy                 | Accurately describes the feature                   | Overpromises (implies contract lifecycle)      |
| Brand alignment                | "Pacto" (pact) + "Signa" (sign) = signing pacts    | Too generic                                    |

**Decision**: Use **"Signing Request"** as the feature name.

### Ubiquitous Language Additions

| Term                       | Definition                                                                                                                                                                                              |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Signing Request**        | A request from an Organization Member for one or more external parties to electronically sign a set of documents. Contains SigningDocuments, designated signers, positioned fields, and an expiry date. |
| **SigningDocument**        | A PDF file uploaded as part of a Signing Request. Value object — has no independent identity outside its parent SigningRequest. Distinct from the Document Management BC's Document entity.             |
| **Signer**                 | An external party designated to sign a Signing Request. Does not need a PactoSigna account. Identified by name and email.                                                                               |
| **Signing Ceremony**       | The complete process of an external Signer reviewing documents, filling in all assigned fields, and applying their electronic signature, including identity verification. Atomic — all or nothing.      |
| **Signing Link**           | A secure, time-limited URL sent to a Signer that allows them to review documents and complete the signing ceremony.                                                                                     |
| **Field**                  | A positioned marker on a SigningDocument page that a Signer must fill during the signing ceremony. Types: signature, initials, date. Stored as percentage-based coordinates.                            |
| **Field Placement Editor** | The UI component where the sender visually positions Fields onto SigningDocument pages before sending the request.                                                                                      |

---

## Summary of Recommendations

1. **Remove the Auditor role** from the member model. Auditors don't need accounts.
2. **Rename "Contract Management"** to **"Signing Request"** to avoid ISO 13485 conflicts.
3. **Build Signing Requests** as a fully independent bounded context with no cross-BC references.
4. **Use SigningDocument** (not Document) for uploaded PDFs to avoid confusion with Document Management BC.
5. **Build a Field Placement Editor** so senders can position signature, initials, and date fields on specific PDF locations.
6. **Build an embedded PDF signing experience** where external signers see the document inline with highlighted fields.
7. **External signing page** is standalone (no auth required) with Part 11 open-system controls.
8. **The "Export for Auditors" button** is renamed to "Export Release Package" — no integration with Signing Requests.
