# Bounded Contexts

This document defines the bounded contexts (subdomains) in PactoSigna and their relationships.

---

## Context Map

```
┌──────────────────────────────────────────────────────────────────────────────────────┐
│                                  CONTEXT MAP                                          │
├──────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                       │
│  ┌───────────────────┐      ┌───────────────────┐         ┌───────────────┐          │
│  │ Identity &        │      │ Repository &      │         │ Document      │          │
│  │ Access            │─────▶│ Owner Management  │────────▶│ Management    │          │
│  │                   │      │                   │         │               │          │
│  │ • Organization    │      │ • Repository      │         │ • Document    │          │
│  │ • Member          │      │ • QMS             │         │ • Link        │          │
│  │ • Invite          │      │ • Device          │         │ • Risk        │          │
│  └──────┬────────────┘      └───────────────────┘         └───────┬───────┘          │
│         │                                                         │                  │
│         │                                                         ▼                  │
│         │                   ┌───────────────────┐         ┌───────────────┐          │
│         │                   │ Training          │◀────────│ Release       │          │
│         │                   │ Compliance        │         │ Management    │          │
│         │                   │                   │         │               │          │
│         │                   │ • TrainingTask    │         │ • Release     │          │
│         │                   │ • QuizSession     │         │ • Signature   │          │
│         │                   └───────────────────┘         │ • ChangeCntrl │          │
│         │                                                 └───────────────┘          │
│         │                   ┌───────────────────┐                                    │
│         ├──────────────────▶│ Signing Requests  │                                    │
│         │                   │                   │                                    │
│         │                   │ • SigningRequest   │                                    │
│         │                   │ • Signer           │                                    │
│         │                   │ • SignerToken       │                                    │
│         │                   └───────────────────┘                                    │
│         │                                                                            │
│         │                   ┌───────────────────┐                                    │
│         ├──────────────────▶│ Notifications      │                                    │
│         │                   │                   │                                    │
│         │                   │ • Notification     │                                    │
│         │                   └───────────────────┘                                    │
│         │                                                                            │
│         │         ┌──────────────────────────────────────────────────────┐            │
│         └────────▶│ Audit (Shared Kernel)                               │            │
│                   │ • AuditLogEntry                                     │            │
│                   └──────────────────────────────────────────────────────┘            │
│                                                                                       │
└──────────────────────────────────────────────────────────────────────────────────────┘
```

---

## 1. Identity & Access Context

**Purpose**: Manages organizations, user membership, and access control.

**Aggregates**:

- **Organization**: Tenant boundary, contains settings for frameworks, reviewer rules
- **Member**: User's membership within an organization (role, department, status)
- **Invite**: Pending invitation to join an organization

**Key Behaviors**:

- Create organization with initial admin member
- Invite users via email with secure token
- Assign roles (admin, member) and departments
- Verify organization membership for all operations

**Code Location**: `apps/services/src/modules/identity/`

---

## 2. Repository & Owner Management Context

**Purpose**: Manages GitHub repository connections and their owners (QMS or Device).

**Aggregates**:

- **Repository**: Connected GitHub repository with sync status
- **QMS**: Organization-wide Quality Management System scope
- **Device**: Medical device with safety class and DHF documentation

**Key Behaviors**:

- Connect GitHub repositories via GitHub App
- Assign repository to QMS or Device owner
- Manage device safety classifications (A, B, C)
- Track repository sync status and errors

**Relationships**:

- Repository must have exactly one owner (QMS or Device)
- QMS is unique per organization
- Device has safety class determining documentation rigor

**Code Location**: `apps/services/src/modules/repositories/`, `apps/services/src/modules/qms/`, `apps/services/src/modules/devices/`

---

## 3. Document Management Context

**Purpose**: Indexes, stores, and provides traceability for documents from GitHub.

**Aggregates**:

- **Document**: Markdown file with frontmatter metadata (documentId, title, docType, status)
- **Link**: Traceability relationship between documents
- **Risk**: Hazard assessment associated with documents

**Key Behaviors**:

- Sync documents from GitHub on webhook events
- Parse frontmatter for metadata extraction
- Build and validate traceability links
- Track document changelog across commits
- Manage risk assessments and risk matrix

**Document Types**:

- User Needs (UN), Requirements (REQ), Architecture (ARCH)
- Detailed Design (DD), Tests (TEST)
- SOPs, Work Instructions (WI), Policies (POL)
- Usability Risk (UR), Software Risk (SR), Security Risk (SEC)

**Code Location**: `apps/services/src/modules/documents/`, `apps/services/src/modules/risks/`

---

## 4. Release Management Context

**Purpose**: Bundles document changes for formal review and approval workflow.

**Aggregates**:

- **Release**: Bundle of document changes with workflow status
- **Signature**: Part 11 compliant electronic signature
- **ChangeControl**: Individual document change tracking for QMS

**Key Behaviors**:

- Create release with selected document versions (commit SHAs)
- Submit release for departmental review
- Collect Part 11 compliant signatures
- Publish release with immutable snapshot
- Track change controls for QMS documents

**Workflow States**:

```
Draft → In Review → Approved → Published
                ↓
            Withdrawn (back to Draft)
```

**Code Location**: `apps/services/src/modules/releases/`, `apps/services/src/modules/signatures/`, `apps/services/src/modules/changeControls/`

---

## 5. Signing Requests Context

**Purpose**: Manages external document signing workflows where signers outside the organization review and sign PDFs via secure token-based links.

**Aggregates**:

- **SigningRequest**: Aggregate root containing documents to sign, assigned signers, and workflow status
- **SigningDocument**: PDF with positioned signature/initials/date fields
- **Signer**: External participant with name, email, status, and authentication token
- **SignerToken**: Global token for unauthenticated signer access (separate collection)

**Key Behaviors**:

- Create signing request with uploaded PDFs and positioned fields
- Generate unique tokens for each signer (external access without authentication)
- Send invitation and reminder emails via Azure Graph API
- Capture signatures, initials, and dates from external signers
- Generate signed PDFs with embedded signatures
- Expire requests automatically after deadline (via scheduled function)
- Comprehensive audit trail per request (created, sent, viewed, signed, declined, cancelled, expired, reminded)

**Workflow States**:

```
Sent → Partially Signed → Completed
                        → Expired (if deadline passes)
       → Cancelled (by creator at any point)
```

**Part 11 Compliance**:

- Meaning selection enforced on request creation
- Email verification for signer identity
- Immutable signatures once captured
- Request-scoped and organization-level audit trail entries
- 30-day maximum expiration window

**Relationships**:

- Depends on Identity & Access (creator must be org member)
- Uses Audit (shared kernel) for organization-level audit entries
- Independent from Release Management (signing requests are standalone workflows)

**Code Location**: `apps/services/src/modules/signing-requests/`, `apps/functions/src/signing-requests/`

---

## 6. Training Compliance Context

**Purpose**: Manages training assignments triggered by published releases, ensuring team members review and acknowledge document changes.

**Aggregates**:

- **TrainingTask**: Assignment for a member to review document changes from a release. Contains status tracking (pending, in_progress, completed, overdue), linked document IDs, and completion evidence.
- **QuizSession**: Assessment session where a member answers questions about document changes. Quiz definitions (questions, correct answers, passing threshold) are stored server-side only (ADR-018). Client never receives correct answers.

**Key Behaviors**:

- Auto-assign training tasks when a release is published (via `trainingAssignmentWorker` Cloud Task)
- Members complete training by reviewing documents and passing a quiz
- Quiz grading is entirely server-side (Part 11 compliance)
- Signature validation on task completion requires existence + ownership + context checks (ADR-019)
- Training status tracked per member per document

**Workflow States**:

```
Pending → In Progress → Completed
                      → Overdue (if deadline passes)
```

**Code Location**: `apps/services/src/modules/training/`, `apps/functions/src/trainingAssignmentWorker.ts`

---

## 7. Notifications Context

**Purpose**: Delivers in-app and email notifications to Members on domain events (training assignment, overdue training, release submission, release publish, signature request).

**Aggregates**:

- **Notification**: In-app and email message scoped to a recipient, with type, body, deep link, and read/email delivery status

**Key Behaviors**:

- Create notification and send email on domain events
- Mark notification as read
- Mark all notifications as read for a recipient
- List notifications per recipient with unread filter
- Scheduled overdue training checks (daily CRON via `trainingOverdueWorker`)

**Integration Points**:

- **Release Management**: Sends notifications on release submitted for review and release published
- **Training Compliance**: Sends notifications on training assigned and training overdue
- **Signature Collection**: Sends notifications on signature requested

**Code Location**: `packages/data/src/services/NotificationService.ts`, `packages/data/src/repositories/NotificationsRepository.ts`, `apps/services/src/modules/notifications/`, `apps/functions/src/trainingOverdueWorker.ts`

---

## 8. Audit Context (Shared Kernel)

**Purpose**: Cross-cutting concern providing immutable audit trail for all operations.

**Aggregates**:

- **AuditLogEntry**: Immutable record of action with before/after state

**Key Behaviors**:

- Record all create, update, delete operations
- Capture user identity and server timestamp
- Store before/after state for changes
- Support querying by resource, user, time range

**Implementation Note**: Audit is a shared kernel - all other contexts depend on it for compliance.

**Code Location**: `apps/services/src/modules/audit/`

---

## Context Relationships

| Upstream Context        | Downstream Context  | Relationship Type    |
| ----------------------- | ------------------- | -------------------- |
| Identity & Access       | All contexts        | Shared Kernel (auth) |
| Repository & Owner Mgmt | Document Management | Customer/Supplier    |
| Document Management     | Release Management  | Customer/Supplier    |
| Release Management      | Training Compliance | Customer/Supplier    |
| Release Management      | Notifications       | Customer/Supplier    |
| Training Compliance     | Notifications       | Customer/Supplier    |
| Identity & Access       | Signing Requests    | Customer/Supplier    |
| Audit                   | All contexts        | Shared Kernel        |

---

## Code Location Reference

| Context                 | API Module                                                              | Data Models                                                     |
| ----------------------- | ----------------------------------------------------------------------- | --------------------------------------------------------------- |
| Identity & Access       | `apps/services/src/modules/identity/`                                   | `packages/data/src/models/organization*, member*, invite*`      |
| Repository & Owner Mgmt | `apps/services/src/modules/repositories/`, `qms/`, `devices/`           | `packages/data/src/models/repository*, qms*, device*`           |
| Document Management     | `apps/services/src/modules/documents/`, `risks/`                        | `packages/data/src/models/document*, risk*`                     |
| Release Management      | `apps/services/src/modules/releases/`, `signatures/`, `changeControls/` | `packages/data/src/models/release*, signature*, changeControl*` |
| Signing Requests        | `apps/services/src/modules/signing-requests/`                           | `packages/data/src/models/signingRequestModels.ts`              |
| Training Compliance     | `apps/services/src/modules/training/`                                   | `packages/data/src/models/training*, quiz*`                     |
| Notifications           | `apps/services/src/modules/notifications/`                              | `packages/data/src/models/notificationModels.ts`                |
| Audit                   | `apps/services/src/modules/audit/`                                      | `packages/data/src/repositories/AuditRepository.ts`             |
