# Ubiquitous Language

This glossary defines the shared vocabulary used across the PactoSigna domain. All team members, documentation, and code should use these terms consistently.

---

## Core Business Terms

### Organization

The **tenant boundary** in PactoSigna. All data (members, repositories, documents, releases) belongs to exactly one organization. Equivalent to a "company" or "team" in other systems.

### Member

A **user within an organization**. Distinct from Firebase Auth users—a person may have one Firebase account but be a Member in multiple organizations. Each Member has:

- A **Department** (e.g., Engineering, QA, Regulatory)
- A **Role** (Admin, Member)
- A **Status** (Active, Deactivated)

### Department

An organizational unit that groups Members by function. Departments are used for:

- Signature requirements (e.g., "QA must sign")
- Access control (future)
- Workflow routing

### Invite

A **pending membership offer**. Contains a unique token that allows a user to join an organization with a specific department and role. Invites expire after 7 days.

---

## Repository & Owner Management Terms

### Repository

A **connected GitHub repository** that contains documentation. Repositories must be "owned" by either a QMS or a Device to enable document processing and release workflows.

### QMS (Quality Management System)

An **organization-wide entity** that owns repositories containing quality system documentation (SOPs, work instructions, procedures). There is exactly one QMS per organization.

### Device

A **medical device product** with associated Design History File (DHF) documentation. Devices have:

- A **Safety Class** (A, B, or C per ISO 14971)
- A linked Repository containing device documentation
- Associated Releases for documentation approval

### Safety Class

ISO 14971 risk classification for medical devices:

- **Class A**: No injury or minor injury
- **Class B**: Non-serious injury
- **Class C**: Serious injury or death

Used to determine documentation rigor and approval requirements.

### Owner

Either a QMS or a Device that "owns" a Repository. Determines which Release workflows apply and what validation rules are enforced.

---

## Document Terms

### Document

A **markdown file with frontmatter metadata** stored in a connected GitHub repository. Documents are indexed into Firestore but content is fetched from GitHub on demand. Key metadata includes:

- **Document ID**: Unique identifier within a repository (from frontmatter `id` field)
- **Doc Type**: Classification (e.g., SOP, WI, SPEC, RISK)
- **Status**: Lifecycle state (Draft, Effective, Obsolete)

### Link

A **traceability relationship** between two documents. Link types include:

- `derives-from`: Requirement derivation
- `implements`: Design implements requirement
- `verified-by`: Test verifies implementation
- `mitigates`: Control mitigates risk
- `parent-of`: Hierarchical relationship

### Document Sync

The process of **indexing document metadata from GitHub** into Firestore. Triggered by webhooks or manual refresh. Only metadata is synced—content is fetched on demand.

---

## Risk Management Terms

### Risk

A potential hazard associated with a Device. Risk records include:

- **Hazard**: Description of the potential harm
- **Severity**: Impact if the hazard occurs (1-5 scale)
- **Probability**: Likelihood of occurrence (1-5 scale)
- **Risk Level**: Calculated from severity × probability
- **Mitigations**: Links to control documents

### Risk Matrix

A visual representation of risk levels across severity and probability dimensions. Generated as part of Release Snapshots for traceability audits.

### Mitigation

A **control measure** that reduces risk. Represented as a Link from a Risk document to a control document (procedure, design control, etc.).

---

## Release Terms

### Release

A **bundle of document changes** that goes through an approval workflow. Releases capture a point-in-time snapshot for regulatory compliance. Types:

- **QMS Release**: Organization-wide quality system changes
- **Device Release**: Device-specific DHF changes

### Release Status

- **Draft**: Changes being collected
- **In Review**: Awaiting signatures
- **Approved**: All signatures collected
- **Published**: Finalized and immutable

### Signature

A **Part 11 compliant electronic signature**. Includes:

- Signer identity (user, email, department)
- Meaning (why they're signing)
- Timestamp (server-generated)
- Signed commit SHAs (what they're attesting to)

Signature types:

- **Author**: Document author attestation
- **Department Reviewer**: Subject matter expert approval
- **Final Approver**: Release authority sign-off

### Change Control

A **formal record of a document change** in QMS context. Used for individual document updates outside of Device releases. Includes change description, version tracking, and approval workflow.

### Release Snapshot

The **frozen state** captured when a Release is published. Includes:

- All document content at that moment (DocumentSnapshots)
- Complete traceability matrix
- Gap analysis results
- Risk matrix (for device releases)

### Document Snapshot

The **captured state of a single document** within a Release. Includes content, metadata, and commit SHA at the time of publication.

### Product Release

A **device-scoped release** that bundles document changes for a specific medical device. Tracks the device's DHF documentation through approval and publication.

---

## Signing Request Terms

### Signing Request

A **workflow for collecting signatures on PDF documents from external signers**. Unlike Release Signatures (which are internal and tied to document approval workflows), Signing Requests target people outside the organization who access documents via secure token-based links.

### External Signer

A **person outside the organization** who is invited to sign documents via a Signing Request. Signers authenticate via email verification, not Firebase Auth. Each signer receives a unique token for access.

### Signing Field

A **positioned region on a PDF page** where a signer must provide input. Field types:

- **Signature**: Hand-drawn signature capture
- **Initials**: Hand-drawn initials capture
- **Date**: Date selection

### Signer Token

A **unique authentication token** for external signer access. Stored in a global Firestore collection (not org-scoped) to enable unauthenticated URL-based access. Maps to a specific signing request and signer email.

### Signing Request Status

- **Sent**: All signers notified, awaiting signatures
- **Partially Signed**: Some signers have completed
- **Completed**: All signers have signed
- **Expired**: Deadline passed before completion
- **Cancelled**: Creator cancelled the request

---

## Training Compliance Terms

### Training Task

An **assignment for a member** to review and acknowledge document changes from a published release. Training tasks are auto-assigned by the `trainingAssignmentWorker` when a release is published. Each task tracks:

- **Status**: Pending, In Progress, Completed, Overdue
- **Assigned Member**: The member who must complete training
- **Linked Documents**: The documents from the release that must be reviewed
- **Completion Evidence**: Quiz session ID and signature ID

### Quiz Session

An **assessment session** where a member answers questions about document changes to demonstrate understanding. Key properties:

- **Quiz Definition**: Stored server-side only (ADR-018) -- client never receives correct answers
- **Passing Threshold**: Server-side configuration (typically 80%)
- **Grading**: Calculated entirely server-side for Part 11 compliance

### Quiz Definition

The **server-side template** containing questions, answer options, and correct answers for a training quiz. Never sent to the client -- only question text and options are exposed. Correct answers and scoring logic remain server-side per ADR-018.

### Training Assignment

The **automated process** of creating training tasks for affected team members when a release is published. Handled by the `trainingAssignmentWorker` Cloud Task.

---

## Notification Terms

### Notification

An **in-app and email message** delivered to a Member when an event relevant to their work occurs. Notifications are scoped to a recipient and link to the relevant resource via a deep link.

### Notification Type

One of: `training_assigned`, `training_overdue`, `release_submitted_for_review`, `release_published`, `signature_requested`. Determines the notification template and routing behavior.

### Deep Link

A **relative URL path** embedded in a notification that navigates the recipient to the relevant resource (e.g., a training task or release). Stored as a path, not a full URL.

---

## Audit Terms

### Audit Log

An **immutable record** of all significant actions in the system. Every create, update, delete operation generates an audit entry with:

- Actor (who)
- Action (what)
- Resource (on what)
- Timestamp (when)
- Before/After state (details)

---

## Technical Terms

### Firebase Auth User

The authentication identity in Firebase. Separate from Member—represents the person across all organizations they belong to.

### Installation ID

The **GitHub App installation identifier** that grants PactoSigna access to an organization's repositories. Stored on Organization.

### Sync Status

The state of a Repository's document sync:

- **Idle**: No sync in progress
- **Syncing**: Sync in progress
- **Error**: Last sync failed

### Commit SHA

A Git commit hash that uniquely identifies a version of repository content. Used to track exactly what version of documents was signed.

### Document ID vs Firestore ID

Documents have **two distinct identifiers** that must not be confused:

- **Firestore ID** (`doc.id`): Auto-generated random string used for internal references
- **Document ID** (`doc.documentId`): User-friendly ID from markdown frontmatter (e.g., `UN-001`, `SRS-042`) used in URLs and user-facing display

**Critical rule**: Frontend URLs use the frontmatter `documentId`. API endpoints receiving IDs from URL params must use `findByDocumentId()`, not `findById()`.

### ViewModel Hook

The **target frontend data fetching pattern**. A React Query custom hook that acts as a ViewModel, encapsulating data fetching, error handling, and state transformation. Pages compose these hooks and render -- no useState + useEffect data fetching in page components.

### App Shell

The **provider chain** that wraps all authenticated content: `AuthProvider → OrganizationProvider → RepositoryProvider → DashboardLayout → Pages`. Each layer depends on its parent resolving before it can proceed. See SDD Frontend Architecture for the loading protocol.

### Cloud Task Worker

A **background processing function** triggered via Google Cloud Tasks queue or Cloud Scheduler. Used for async operations: document sync (`syncWorker`), PDF export (`exportWorker`), training assignment (`trainingAssignmentWorker`), signed PDF generation (`signedPdfWorker`), signing notifications (`signingNotificationWorker`), and signing expiry checks (`signingExpiryChecker`).

---

## Bounded Context Mapping

| Term            | Primary Context         | Notes                         |
| --------------- | ----------------------- | ----------------------------- |
| Organization    | Identity & Access       | Tenant boundary               |
| Member          | Identity & Access       | User + org membership         |
| Department      | Identity & Access       | Org subdivision               |
| Invite          | Identity & Access       | Pending membership            |
| Repository      | Repository & Owner Mgmt | Connected GitHub repo         |
| QMS             | Repository & Owner Mgmt | Quality system owner          |
| Device          | Repository & Owner Mgmt | Medical device owner          |
| Safety Class    | Repository & Owner Mgmt | ISO 14971 classification      |
| Document        | Document Management     | Indexed markdown file         |
| Link            | Document Management     | Traceability relationship     |
| Risk            | Document Management     | Hazard + controls             |
| Release         | Release Management      | Document bundle for approval  |
| Signature       | Release Management      | Part 11 compliant e-sig       |
| Change Control  | Release Management      | QMS document change record    |
| ProductRelease  | Release Management      | Device-scoped release         |
| Signing Request | Signing Requests        | External PDF signing workflow |
| External Signer | Signing Requests        | Non-org participant           |
| Signing Field   | Signing Requests        | Positioned input on PDF       |
| Signer Token    | Signing Requests        | External auth token           |
| Training Task   | Training Compliance     | Member training assignment    |
| Quiz Session    | Training Compliance     | Assessment for training       |
| Quiz Definition | Training Compliance     | Server-side quiz template     |
| Notification    | Notifications           | In-app + email message        |
| Deep Link       | Notifications           | Relative URL to resource      |
| Audit Log       | Audit (Shared Kernel)   | Immutable action record       |

---

## Anti-Patterns (Terms to Avoid)

| Don't Say | Say Instead     | Why                                              |
| --------- | --------------- | ------------------------------------------------ |
| User      | Member          | User is ambiguous—Member is org-scoped           |
| Project   | Repository      | Project is overloaded—Repository is specific     |
| File      | Document        | File is too generic—Document has metadata        |
| Approval  | Signature       | We use Part 11 signatures, not generic approvals |
| Version   | Commit SHA      | Version is ambiguous—commit SHA is precise       |
| Product   | Device          | Product could mean anything—Device is specific   |
| Record    | Audit Log Entry | Record is generic—Audit Log Entry is precise     |
