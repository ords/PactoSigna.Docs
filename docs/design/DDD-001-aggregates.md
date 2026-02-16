# Aggregates & Entities

This document defines the aggregate roots, entities, and value objects for each bounded context.

> **Source of Truth**: The authoritative type definitions are in `packages/data/src/models/`. This document provides a summary with key invariants.

---

## Aggregate Summary

| Aggregate Root   | Bounded Context         | Model File                       | Key Invariants                                  |
| ---------------- | ----------------------- | -------------------------------- | ----------------------------------------------- |
| Organization     | Identity & Access       | `organizationModels.ts`          | Must have at least one admin member             |
| Member           | Identity & Access       | `memberModels.ts`                | Must belong to exactly one department           |
| Invite           | Identity & Access       | `inviteModels.ts`                | Single-use, expires after 7 days                |
| Repository       | Repository & Owner Mgmt | `repositoryModels.ts`            | Must be owned by exactly one QMS or Device      |
| QMS              | Repository & Owner Mgmt | `qmsModels.ts`                   | One per organization                            |
| Device           | Repository & Owner Mgmt | `deviceModels.ts`                | Must have safety class (A, B, or C)             |
| Document         | Document Management     | `documentModels.ts`              | Unique documentId within repository             |
| Link             | Document Management     | (in documentModels.ts)           | Source and target must exist                    |
| Release          | Release Management      | `releaseModels.ts`               | Cannot publish without all required signatures  |
| Signature        | Release Management      | `signatureModels.ts`             | Immutable once created                          |
| ChangeControl    | Release Management      | `changeControlModels.ts`         | Requires approval before effective              |
| SigningRequest   | Signing Requests        | `signingRequestModels.ts`        | Cannot complete without all signers signing     |
| VerificationCode | Signing Requests        | `VerificationCodesRepository.ts` | Max 5 attempts, 10-min TTL                      |
| Notification     | Notifications           | `notificationModels.ts`          | recipientId required, deepLink is relative path |
| AuditLogEntry    | Audit (Shared Kernel)   | (in AuditRepository.ts)          | Immutable, server-timestamped                   |

---

## 1. Identity & Access Context

### Organization

The tenant boundary. All data is scoped to an organization.

**Invariants:**

- Must have at least one admin member
- Name must be unique (soft constraint)
- GitHub App installation is optional but required for document sync

**Key Fields:**

- `id`, `name`, `settings` (frameworks, reviewer rules, default safety class)
- `gitHubAppInstallationId`, `gitHubOrgName` (optional)

### Member

A user's membership in an organization. Separate from Firebase Auth user.

**Invariants:**

- Must belong to exactly one department
- Email must be unique within organization
- Cannot delete last admin

**Key Fields:**

- `id`, `firebaseUid`, `email`, `displayName`
- `department`, `role` (admin | member), `status`

### Invite

Allows users to join an organization.

**Invariants:**

- Token must be unique
- Cannot be used after expiration
- Single-use (consumed on acceptance)

**Key Fields:**

- `id`, `organizationId`, `email`, `department`, `role`
- `token`, `expiresAt`, `status` (pending | accepted | expired | revoked)

---

## 2. Repository & Owner Management Context

### Repository

A connected GitHub repository.

**Invariants:**

- GitHub repo full name must be unique within organization
- Must be owned by exactly one QMS or Device (`ownerType`, `ownerId`)

**Key Fields:**

- `id`, `organizationId`, `gitHubRepoFullName`, `gitHubRepoId`
- `ownerType` (qms | device | null), `ownerId`
- `syncStatus`, `lastSyncedAt`, `lastSyncCommitSha`

### QMS

Organization-wide quality management system.

**Invariants:**

- One QMS per organization
- Must link to a repository

**Key Fields:**

- `id`, `organizationId`, `repositoryId`, `name`

### Device

Medical device with associated DHF documentation.

**Invariants:**

- Must have safety class (A, B, or C)
- Must link to a repository

**Key Fields:**

- `id`, `organizationId`, `repositoryId`, `name`, `description`
- `safetyClass` (A | B | C)

---

## 3. Document Management Context

### Document

A markdown file with frontmatter metadata, indexed from GitHub.

**Invariants:**

- `documentId` (from frontmatter) must be unique within repository
- Must have a valid `docType`

**Key Fields:**

- `id`, `repositoryId`, `organizationId`, `path`
- `documentId`, `title`, `docType`, `status`
- `currentCommitSha`, `lastSyncedAt`

### Link

Traceability relationship between two documents.

**Invariants:**

- Source and target documents should exist (soft validation)
- Link type must be valid (derives-from, implements, verified-by, mitigates, parent-of)

**Key Fields:**

- `sourceDocumentId`, `targetDocumentId`, `linkType`

---

## 4. Release Management Context

### Release

Bundles document changes for approval workflow.

**Invariants:**

- Cannot transition to Approved without all required department signatures
- Cannot transition to Published without final approver signature
- Published releases are immutable

**Key Fields:**

- `id`, `organizationId`, `name`, `description`
- `type` (qms | device), `deviceId` (for device releases)
- `changes[]` (document snapshots), `requiredDepartments[]`
- `status` (draft | in_review | approved | published)
- `signatures[]`, `snapshot` (captured at publish)

### Signature

Part 11 compliant electronic signature.

**Invariants:**

- Immutable once created
- Must include meaning and server timestamp
- Bound to specific commit SHAs

**Key Fields:**

- `id`, `releaseId`, `userId`, `userEmail`, `userDepartment`
- `signatureType` (author | dept_reviewer | final_approver)
- `meaning`, `timestamp`, `signedCommitShas[]`

### ChangeControl

Tracks individual document changes in QMS context.

**Invariants:**

- Requires approval signatures before becoming effective
- Must reference specific document and commit

**Key Fields:**

- `id`, `qmsId`, `documentId`, `commitSha`
- `changeType` (new | revision | obsolete), `changeDescription`
- `status` (draft | pending_signatures | approved | effective)
- `previousVersion`, `newVersion`, `effectiveDate`

---

## 5. Signing Requests Context

### SigningRequest

Aggregate root for external document signing workflows.

**Invariants:**

- Cannot complete without all signers having signed
- Expiry cannot exceed 30 days from creation
- All signers must have at least one field assigned
- Cancelled/completed/expired requests cannot be modified

**Key Fields:**

- `id`, `organizationId`, `title`, `description`
- `meaning` (predefined compliance statement)
- `status` (sent | partially_signed | completed | expired | cancelled)
- `createdBy`, `createdByEmail`, `createdAt`, `expiresAt`
- `signingDocuments[]` (SigningDocumentModel), `signers[]` (SignerModel)
- `signedDocumentPaths[]` (Cloud Storage paths after completion)

### SigningDocument (Value Object)

A PDF document within a signing request with positioned fields.

**Key Fields:**

- `fileName`, `storagePath`, `fileHash` (SHA-256 integrity)
- `pageCount`, `uploadedAt`
- `fields[]` (SigningFieldModel: id, type, assignedSignerEmail, page, x, y, width, height)

### Signer (Value Object)

An external participant in a signing request.

**Key Fields:**

- `name`, `email`
- `status` (pending | viewed | signed | declined)
- `token` (unique token for external signing link)

### SignerToken (Entity - global collection)

Authentication token for unauthenticated signer access.

**Invariants:**

- Token must be unique globally
- Maps to exactly one signing request and signer

**Key Fields:**

- `token`, `organizationId`, `signingRequestId`, `signerEmail`

### VerificationCode

Short-lived code for identity verification during signing ceremony.

**Invariants:**

- Expires after 10 minutes (TTL)
- Maximum 5 verification attempts before invalidation (brute-force protection)
- One active code per signing token

**Key Fields:**

- `code` (6-digit), `signerEmail`, `signingRequestId`, `organizationId`
- `expiresAt`, `attempts`

---

## 6. Notifications Context

### Notification

In-app and email message delivered to a Member on a domain event.

**Collection**: `organizations/{orgId}/notifications/{notificationId}`

**Invariants:**

- `recipientId` is required
- `deepLink` stores a relative path (not a full URL)
- `emailSent` and `emailSentAt` track delivery status

**Key Fields:**

- `id`, `recipientId`, `recipientEmail`
- `type` (NotificationType: training_assigned | training_overdue | release_submitted_for_review | release_published | signature_requested)
- `title`, `body`
- `resourceType`, `resourceId`, `deepLink`
- `read`, `emailSent`, `emailSentAt`, `createdAt`

**Model File**: `packages/data/src/models/notificationModels.ts`

---

## 7. Audit Context (Shared Kernel)

### AuditLogEntry

Immutable record of a significant action.

**Invariants:**

- Cannot be updated or deleted
- Timestamp is server-generated
- All mutations in the system create an audit entry

**Key Fields:**

- `id`, `organizationId`, `action`, `userId`, `userEmail`
- `resourceType`, `resourceId`, `timestamp`
- `details` (before, after, metadata)

---

## Value Objects

| Value Object           | Context            | Description                                      |
| ---------------------- | ------------------ | ------------------------------------------------ |
| `OrganizationSettings` | Identity & Access  | Frameworks, reviewer rules, default safety class |
| `ReviewerRule`         | Identity & Access  | Document type → required departments mapping     |
| `EmailAddress`         | Identity & Access  | Value + verified flag                            |
| `ReleaseChange`        | Release Management | Document snapshot in a release                   |
| `ReleaseSnapshot`      | Release Management | Traceability, gaps, risk matrix at publish       |
| `DocumentSnapshot`     | Release Management | Document state captured in release               |

---

## Code References

All model definitions are in:

```
packages/data/src/models/
├── organizationModels.ts
├── memberModels.ts
├── inviteModels.ts
├── repositoryModels.ts
├── qmsModels.ts
├── deviceModels.ts
├── documentModels.ts
├── releaseModels.ts
├── signatureModels.ts
├── changeControlModels.ts
├── productReleaseModels.ts
├── signingRequestModels.ts
├── notificationModels.ts
└── riskModels.ts
```

Repository implementations are in:

```
packages/data/src/repositories/
```
