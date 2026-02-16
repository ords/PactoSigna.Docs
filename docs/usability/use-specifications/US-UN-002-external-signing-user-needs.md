# External Signing - Jobs to Be Done

## Job Performers

### Internal: QA/RA Manager (Primary)

The person responsible for managing audits, supplier relationships, and compliance documentation.

### Internal: Release Manager

The person who publishes releases and needs to send finalized documents for external acknowledgment.

### External: Auditor (Notified Body / FDA / Internal Consultant)

An independent party who reviews QMS documentation and signs audit findings.

### External: Supplier / Contractor

An external business partner who needs to sign quality agreements or deliverables.

---

## Main Jobs

### Job 1: Send Published Release to Auditor for Acknowledgment

**When** a release is published and an audit is scheduled,
**I want to** send the complete release package to the auditor for their signed acknowledgment,
**So that** I have documented evidence that the auditor received and reviewed the materials.

**Current workaround**: Export release as ZIP, email to auditor, ask them to sign a PDF and email it back. No audit trail. No Part 11 compliance. Manual tracking.

**Success criteria**:

- Auditor receives documents via a secure, trackable mechanism
- Auditor's signature is captured with Part 11 fields (identity, timestamp, meaning)
- I can prove to regulators that the auditor acknowledged receipt
- The complete signing ceremony is recorded in the audit log

### Job 2: Get External Party Signature on a Quality Agreement

**When** onboarding a new supplier or renewing a quality agreement,
**I want to** upload the agreement document and send it for the supplier's signature,
**So that** I have a Part 11 compliant record of the signed agreement without needing a separate DocuSign subscription.

**Current workaround**: Use DocuSign ($25-60/user/month), or email PDFs back and forth. Signed documents stored in SharePoint/Google Drive, disconnected from the QMS.

**Success criteria**:

- Supplier signs without creating a PactoSigna account
- Signed document is linked to the organization's QMS
- Audit trail shows the complete signing ceremony
- I save the cost of a separate e-signature tool

### Job 3: Track Which Releases Have Been Audited

**When** preparing for a surveillance audit or management review,
**I want to** see which published releases have been sent to auditors and whether acknowledgments are complete,
**So that** I can demonstrate audit coverage to regulators.

**Current workaround**: Spreadsheet tracking. Check email for signed PDFs. Hope nothing was missed.

**Success criteria**:

- Signing Requests page shows status per release
- I can filter by "completed" vs "pending" vs "expired"
- Each signing request links back to the original release

### Job 4: Sign Audit Findings as an External Auditor

**When** I've completed my audit and prepared my findings report,
**I want to** sign the report through a secure mechanism that the company provides,
**So that** my signature is properly documented for regulatory purposes.

**Current workaround**: Print, sign, scan, email. Or use DocuSign if the company provides it.

**Success criteria**:

- I don't need to create an account or remember a password
- I receive a single link in my email
- I can review the documents before signing
- The signing process is straightforward (under 2 minutes)
- I receive a copy of the signed document

---

## Switching Triggers

### What makes organizations switch from their current approach?

| Trigger                           | From                                             | To (Signing Requests)                               |
| --------------------------------- | ------------------------------------------------ | --------------------------------------------------- |
| DocuSign cost                     | $25-60/user/month for an additional tool         | Included in PactoSigna subscription                 |
| Audit trail gaps                  | Signed PDFs in email/SharePoint with no QMS link | Signing ceremonies recorded in QMS audit log        |
| Part 11 compliance anxiety        | "Is our DocuSign setup Part 11 compliant?"       | Purpose-built for Part 11 with open system controls |
| Audit preparation pain            | "Where did we put that signed audit report?"     | Signing Requests page with status tracking          |
| Separate QMS account for auditors | Managing auditor accounts, expiry, access        | No account needed -- just a signing link            |

---

## Anxieties (Reasons NOT to Switch)

| Anxiety                                                     | Mitigation                                                                                                                 |
| ----------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| "Is this legally binding?"                                  | Part 11 compliant, audit trail, identity verification                                                                      |
| "What if the signer disputes signing?"                      | Complete audit trail: email sent, link clicked, code verified, IP captured                                                 |
| "30-day retention seems short"                              | Documents are uploaded by the member and stored in Cloud Storage; signed attestation persists in Firestore indefinitely    |
| "Can auditors still browse our published documents?"        | Export packages are comprehensive; if live access is needed, screen sharing during audit sessions is the industry standard |
| "We used to have auditor accounts -- what happens to them?" | Migration: existing auditor members become regular members or are removed; their historical signatures remain valid        |
