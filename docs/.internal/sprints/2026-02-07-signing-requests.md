# Signing Requests Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Build a Signing Request feature that lets Organization Members send PDFs to external parties for Part 11 compliant electronic signatures — with positioned fields (signature, initials, date) on specific PDF locations.

**Architecture:** New "External Signing" bounded context, fully independent from Document Management and Release Management. SigningRequest is the aggregate root with embedded SigningDocument value objects. External signers access a standalone unauthenticated page via secure token links. PDF field placement editor on sender side; embedded PDF viewer with fillable fields on signer side.

**Tech Stack:** `@react-pdf-viewer/core` (frontend PDF rendering), `react-signature-canvas` (signature/initials capture), `pdf-lib` (backend PDF overlay for signed copies), existing Fastify + Firestore + Cloud Storage + Microsoft Graph email.

**Design docs:** `docs/ux/external-signing/` (redesign-proposal.md, user-journey.md, terminology.md, jtbd.md, ux-research.md)

---

## Phase 1: Remove Auditor Role

Phase 1 cleans up the existing auditor references before building the new feature.

---

### Task 1: Remove auditor from shared types and schemas

**Files:**

- Modify: `packages/shared/src/types/member.ts:7`
- Modify: `packages/shared/src/schemas/index.ts` (find `auditor` in Zod enum)

**Step 1: Find all auditor references in packages/shared**

Run: `grep -rn "auditor" packages/shared/src/`

Document every file and line that references 'auditor'.

**Step 2: Remove auditor from MemberRole**

In `packages/shared/src/types/member.ts`, change:

```typescript
export type MemberRole = 'admin' | 'member' | 'auditor';
```

To:

```typescript
export type MemberRole = 'admin' | 'member';
```

**Step 3: Remove auditor from Zod schemas**

In `packages/shared/src/schemas/index.ts`, find any `z.enum` or `z.union` containing `'auditor'` and remove it.

**Step 4: Run typecheck to find all downstream breakage**

Run: `pnpm typecheck`

Expected: TypeScript errors in files that reference `'auditor'` as a valid role value. Document all errors — these become tasks 2-4.

**Step 5: Commit**

```bash
git add packages/shared/src/types/member.ts packages/shared/src/schemas/index.ts
git commit -m "refactor: remove auditor from MemberRole type and schemas"
```

---

### Task 2: Remove auditor from contracts and data layer

**Files:**

- Modify: `packages/contracts/src/entities.ts` (remove auditor from role)
- Modify: `packages/contracts/src/identity/requests.ts` (remove auditor from RoleSchema)
- Modify: `packages/data/src/models/memberModels.ts` (remove auditor from role)

**Step 1: Find all auditor references in contracts and data**

Run: `grep -rn "auditor" packages/contracts/src/ packages/data/src/`

**Step 2: Remove auditor from each file**

In each file identified, remove `'auditor'` from any type union, Zod enum, or string literal.

**Step 3: Run typecheck**

Run: `pnpm typecheck`

Expected: Fewer errors than before. Remaining errors should be in apps/ layer.

**Step 4: Commit**

```bash
git add packages/contracts/ packages/data/
git commit -m "refactor: remove auditor from contracts and data layer"
```

---

### Task 3: Remove auditor from backend services

**Files:**

- Modify: `apps/services/src/modules/releases/ReleasesService.ts` (remove auditor signature logic)
- Modify: `apps/functions/src/export/attestation.ts` (remove auditor from signature type formatter)

**Step 1: Find all auditor references in backend**

Run: `grep -rn "auditor" apps/services/src/ apps/functions/src/`

**Step 2: Remove auditor-specific logic**

In ReleasesService.ts, remove any conditional branches for auditor signature type. In attestation.ts, remove auditor from any display label mapping.

**Step 3: Run tests**

Run: `pnpm --filter @pactosigna/services test`
Run: `pnpm --filter @pactosigna/functions test`

Fix any test failures caused by the removal.

**Step 4: Run typecheck**

Run: `pnpm typecheck`

Expected: No type errors in packages/ or backend apps/.

**Step 5: Commit**

```bash
git add apps/services/ apps/functions/
git commit -m "refactor: remove auditor signature logic from backend"
```

---

### Task 4: Remove auditor from frontend

**Files:**

- Modify: `apps/web/src/features/signatures/components/SignaturePanel.tsx` (remove auditor label)
- Modify: `apps/web/src/features/settings/pages/OrganizationSettingsPage.tsx` (remove auditor from role dropdown)
- Modify: `apps/web/src/features/releases/pages/ReleaseDetailPage.tsx` (rename "Export for Auditors" to "Export Release Package")

**Step 1: Find all auditor references in frontend**

Run: `grep -rn "auditor\|Auditor" apps/web/src/`

**Step 2: Remove auditor from each file**

- SignaturePanel.tsx: Remove any auditor-specific label or chip
- OrganizationSettingsPage.tsx: Remove 'auditor' from role dropdown options
- ReleaseDetailPage.tsx: Change button label from "Export for Auditors" to "Export Release Package"

**Step 3: Run frontend tests**

Run: `pnpm --filter @pactosigna/web test`

Fix any test failures.

**Step 4: Run full typecheck**

Run: `pnpm typecheck`

Expected: Clean — zero type errors across the entire monorepo.

**Step 5: Commit**

```bash
git add apps/web/
git commit -m "refactor: remove auditor role from frontend, rename export button"
```

---

### Task 5: Update ADRs and domain documentation

**Files:**

- Modify: `docs/architecture/adr/adr-005-department-based-permissions.md` (remove Auditor column from permission matrix)
- Modify: `docs/architecture/adr/adr-007-hybrid-signature-flow.md` (remove auditor signature type)
- Modify: `docs/domain/ubiquitous-language.md` (if auditor is defined there)

**Step 1: Update ADR-005**

Remove the Auditor column from the permission matrix table. Add a note: "Auditor role removed in Phase 1 of Signing Requests feature. See docs/ux/external-signing/redesign-proposal.md."

**Step 2: Update ADR-007**

Remove auditor signature type from the signature flow description. Add same note.

**Step 3: Check ubiquitous language**

Run: `grep -rn "auditor\|Auditor" docs/domain/`

If found, update or remove.

**Step 4: Commit**

```bash
git add docs/
git commit -m "docs: update ADRs to reflect auditor role removal"
```

---

## Phase 2: Build Signing Requests Bounded Context

Phase 2 builds the feature bottom-up: types → contracts → data → service → API → frontend.

---

### Task 6: Create SigningRequest domain types

**Files:**

- Create: `packages/shared/src/types/signing-request.ts`
- Modify: `packages/shared/src/types/index.ts` (add export)

**Step 1: Create the types file**

Create `packages/shared/src/types/signing-request.ts`:

```typescript
/**
 * External Signing Context - Signing Request Types
 *
 * Bounded Context: External Signing (fully independent from Document Management)
 * SigningDocument is a value object — NOT related to the Document Management "Document" entity.
 */

// === Status Enums ===

export type SigningRequestStatus =
  | 'sent'
  | 'partially_signed'
  | 'completed'
  | 'expired'
  | 'cancelled';

export type SignerStatus = 'pending' | 'viewed' | 'signed' | 'declined';

export type FieldType = 'signature' | 'initials' | 'date';

// === Value Objects ===

/** A positioned field on a SigningDocument page */
export interface SigningField {
  id: string;
  type: FieldType;
  assignedSignerEmail: string;
  page: number; // 1-indexed
  x: number; // percentage 0-100
  y: number; // percentage 0-100
  width: number; // percentage
  height: number; // percentage
}

/**
 * A PDF uploaded as part of a Signing Request.
 * Value object — no independent identity outside its parent SigningRequest.
 * NOT related to Document Management BC's Document entity.
 */
export interface SigningDocument {
  fileName: string;
  storagePath: string;
  fileHash: string; // SHA-256
  pageCount: number;
  uploadedAt: Date;
  fields: SigningField[];
}

/** An external party designated to sign */
export interface Signer {
  name: string;
  email: string;
  status: SignerStatus;
  token: string; // unique secure token for signing link
}

// === Aggregate Root ===

export interface SigningRequest {
  id: string;
  organizationId: string;
  title: string;
  description?: string;
  meaning: string; // signature attestation text
  status: SigningRequestStatus;
  createdBy: string; // Member userId
  createdByEmail: string;
  createdAt: Date;
  expiresAt: Date;
  signingDocuments: SigningDocument[];
  signers: Signer[];
}

// === Signature Record (captured when signer completes ceremony) ===

export interface FieldValue {
  fieldId: string;
  type: FieldType;
  value: string; // storage ref for images, ISO string for dates
}

export interface SigningRequestSignature {
  id: string;
  signerEmail: string;
  signerName: string;
  signedAt: Date;
  ipAddress: string;
  meaning: string;
  identityVerification: {
    method: 'email_code';
    verifiedAt: Date;
  };
  signatureImagePath: string; // Cloud Storage path
  initialsImagePath: string; // Cloud Storage path
  fieldValues: FieldValue[];
}

// === Audit Trail Entry ===

export type SigningRequestAuditEventType =
  | 'created'
  | 'sent'
  | 'viewed'
  | 'signed'
  | 'declined'
  | 'cancelled'
  | 'expired'
  | 'reminded';

export interface SigningRequestAuditEntry {
  id: string;
  eventType: SigningRequestAuditEventType;
  actor: string; // member userId or signer email
  timestamp: Date;
  details?: Record<string, unknown>;
}

// === Predefined Signature Meanings ===

export const PREDEFINED_MEANINGS = [
  'I acknowledge receipt of this audit report',
  'I have reviewed the enclosed documents',
  'I agree to the terms of this quality agreement',
] as const;
```

**Step 2: Export from index**

Add to `packages/shared/src/types/index.ts`:

```typescript
// External Signing Context
export * from './signing-request.js';
```

**Step 3: Run typecheck**

Run: `pnpm --filter @pactosigna/shared typecheck`

Expected: PASS

**Step 4: Commit**

```bash
git add packages/shared/src/types/signing-request.ts packages/shared/src/types/index.ts
git commit -m "feat: add SigningRequest domain types"
```

---

### Task 7: Create SigningRequest contracts (Zod schemas)

**Files:**

- Create: `packages/contracts/src/signing-requests/requests.ts`
- Create: `packages/contracts/src/signing-requests/responses.ts`
- Create: `packages/contracts/src/signing-requests/index.ts`
- Modify: `packages/contracts/src/index.ts` (add export)

**Step 1: Create request schemas**

Create `packages/contracts/src/signing-requests/requests.ts`:

```typescript
import { z } from 'zod';

// === Enums ===

export const SigningRequestStatusSchema = z.enum([
  'sent',
  'partially_signed',
  'completed',
  'expired',
  'cancelled',
]);
export type SigningRequestStatus = z.infer<typeof SigningRequestStatusSchema>;

export const SignerStatusSchema = z.enum(['pending', 'viewed', 'signed', 'declined']);
export type SignerStatus = z.infer<typeof SignerStatusSchema>;

export const FieldTypeSchema = z.enum(['signature', 'initials', 'date']);
export type FieldType = z.infer<typeof FieldTypeSchema>;

// === Field Schema ===

export const SigningFieldSchema = z.object({
  id: z.string().min(1),
  type: FieldTypeSchema,
  assignedSignerEmail: z.string().email(),
  page: z.number().int().positive(),
  x: z.number().min(0).max(100),
  y: z.number().min(0).max(100),
  width: z.number().min(0).max(100),
  height: z.number().min(0).max(100),
});

// === Signing Document Schema (for request body) ===

export const SigningDocumentInputSchema = z.object({
  fileName: z.string().min(1),
  storagePath: z.string().min(1),
  fileHash: z.string().min(1),
  pageCount: z.number().int().positive(),
  fields: z.array(SigningFieldSchema).min(1),
});

// === Signer Schema ===

export const SignerInputSchema = z.object({
  name: z.string().min(1).max(200),
  email: z.string().email(),
});

// === Path Params ===

export const SigningRequestIdParamSchema = z.object({
  signingRequestId: z.string().min(1),
});
export type SigningRequestIdParam = z.infer<typeof SigningRequestIdParamSchema>;

// === Query Schemas ===

export const ListSigningRequestsQuerySchema = z.object({
  organizationId: z.string().min(1),
  status: SigningRequestStatusSchema.optional(),
  limit: z.coerce.number().int().positive().max(100).default(50),
  offset: z.coerce.number().int().nonnegative().default(0),
});
export type ListSigningRequestsQuery = z.infer<typeof ListSigningRequestsQuerySchema>;

export const GetSigningRequestQuerySchema = z.object({
  organizationId: z.string().min(1),
});
export type GetSigningRequestQuery = z.infer<typeof GetSigningRequestQuerySchema>;

// === Body Schemas ===

export const CreateSigningRequestSchema = z.object({
  organizationId: z.string().min(1),
  title: z.string().min(1).max(200),
  description: z.string().max(2000).optional(),
  meaning: z.string().min(1).max(500),
  expiresAt: z.string().datetime(), // ISO string, validated server-side for max 30 days
  signingDocuments: z.array(SigningDocumentInputSchema).min(1),
  signers: z.array(SignerInputSchema).min(1),
});
export type CreateSigningRequestBody = z.infer<typeof CreateSigningRequestSchema>;

export const CancelSigningRequestSchema = z.object({
  organizationId: z.string().min(1),
});
export type CancelSigningRequestBody = z.infer<typeof CancelSigningRequestSchema>;

export const RemindSignersSchema = z.object({
  organizationId: z.string().min(1),
});
export type RemindSignersBody = z.infer<typeof RemindSignersSchema>;

// === External Signer Schemas (unauthenticated) ===

export const GetSigningCeremonyParamSchema = z.object({
  token: z.string().min(1),
});
export type GetSigningCeremonyParam = z.infer<typeof GetSigningCeremonyParamSchema>;

export const RequestVerificationCodeSchema = z.object({
  token: z.string().min(1),
});
export type RequestVerificationCodeBody = z.infer<typeof RequestVerificationCodeSchema>;

export const CompleteSigningCeremonySchema = z.object({
  token: z.string().min(1),
  verificationCode: z.string().length(6),
  signatureImagePath: z.string().min(1),
  initialsImagePath: z.string().min(1),
  fieldValues: z
    .array(
      z.object({
        fieldId: z.string().min(1),
        type: FieldTypeSchema,
        value: z.string().min(1),
      })
    )
    .min(1),
});
export type CompleteSigningCeremonyBody = z.infer<typeof CompleteSigningCeremonySchema>;
```

**Step 2: Create response schemas**

Create `packages/contracts/src/signing-requests/responses.ts`:

```typescript
import { z } from 'zod';
import { SigningRequestStatusSchema, SignerStatusSchema, FieldTypeSchema } from './requests.js';

// === Field Response ===

export const SigningFieldResponseSchema = z.object({
  id: z.string(),
  type: FieldTypeSchema,
  assignedSignerEmail: z.string(),
  page: z.number(),
  x: z.number(),
  y: z.number(),
  width: z.number(),
  height: z.number(),
});

// === Signing Document Response ===

export const SigningDocumentResponseSchema = z.object({
  fileName: z.string(),
  storagePath: z.string(),
  fileHash: z.string(),
  pageCount: z.number(),
  uploadedAt: z.string(),
  fields: z.array(SigningFieldResponseSchema),
});

// === Signer Response ===

export const SignerResponseSchema = z.object({
  name: z.string(),
  email: z.string(),
  status: SignerStatusSchema,
  signedAt: z.string().optional(),
});

// === Audit Trail Response ===

export const SigningAuditEntryResponseSchema = z.object({
  id: z.string(),
  eventType: z.string(),
  actor: z.string(),
  timestamp: z.string(),
  details: z.record(z.unknown()).optional(),
});

// === Signing Request Responses ===

export const SigningRequestSummaryResponseSchema = z.object({
  id: z.string(),
  title: z.string(),
  status: SigningRequestStatusSchema,
  meaning: z.string(),
  signerCount: z.number(),
  signedCount: z.number(),
  createdAt: z.string(),
  expiresAt: z.string(),
});
export type SigningRequestSummaryResponse = z.infer<typeof SigningRequestSummaryResponseSchema>;

export const SigningRequestDetailResponseSchema = SigningRequestSummaryResponseSchema.extend({
  description: z.string().optional(),
  createdBy: z.string(),
  createdByEmail: z.string(),
  signingDocuments: z.array(SigningDocumentResponseSchema),
  signers: z.array(SignerResponseSchema),
  auditTrail: z.array(SigningAuditEntryResponseSchema),
});
export type SigningRequestDetailResponse = z.infer<typeof SigningRequestDetailResponseSchema>;

export const ListSigningRequestsResponseSchema = z.object({
  signingRequests: z.array(SigningRequestSummaryResponseSchema),
  total: z.number(),
});
export type ListSigningRequestsResponse = z.infer<typeof ListSigningRequestsResponseSchema>;

// === Signing Ceremony Response (external signer view) ===

export const SigningCeremonyResponseSchema = z.object({
  organizationName: z.string(),
  title: z.string(),
  description: z.string().optional(),
  meaning: z.string(),
  signerName: z.string(),
  signerEmail: z.string(),
  status: z.enum(['ready', 'already_signed', 'expired', 'cancelled']),
  signingDocuments: z.array(SigningDocumentResponseSchema),
  /** Only fields assigned to THIS signer */
  fields: z.array(SigningFieldResponseSchema),
});
export type SigningCeremonyResponse = z.infer<typeof SigningCeremonyResponseSchema>;
```

**Step 3: Create index**

Create `packages/contracts/src/signing-requests/index.ts`:

```typescript
export * from './requests.js';
export * from './responses.js';
```

**Step 4: Export from contracts root**

Add to `packages/contracts/src/index.ts`:

```typescript
export * from './signing-requests/index.js';
```

**Step 5: Run typecheck**

Run: `pnpm --filter @pactosigna/contracts typecheck`

Expected: PASS

**Step 6: Commit**

```bash
git add packages/contracts/src/signing-requests/ packages/contracts/src/index.ts
git commit -m "feat: add SigningRequest API contracts (Zod schemas)"
```

---

### Task 8: Create SigningRequest data models and repository

**Files:**

- Create: `packages/data/src/models/signingRequestModels.ts`
- Modify: `packages/data/src/models/index.ts` (add export)
- Create: `packages/data/src/repositories/SigningRequestsRepository.ts`
- Modify: `packages/data/src/repositories/index.ts` (add export)

**Step 1: Create Firestore models**

Create `packages/data/src/models/signingRequestModels.ts`:

```typescript
import type { Timestamp } from 'firebase-admin/firestore';
import type {
  SigningRequestStatus,
  SignerStatus,
  FieldType,
  SigningRequestAuditEventType,
} from '@pactosigna/shared';

/**
 * Firestore model for SigningField (embedded in SigningDocumentModel)
 */
export interface SigningFieldModel {
  id: string;
  type: FieldType;
  assignedSignerEmail: string;
  page: number;
  x: number;
  y: number;
  width: number;
  height: number;
}

/**
 * Firestore model for SigningDocument (embedded in SigningRequestModel)
 * Value object — NOT related to Document Management's DocumentModel.
 */
export interface SigningDocumentModel {
  fileName: string;
  storagePath: string;
  fileHash: string;
  pageCount: number;
  uploadedAt: Timestamp;
  fields: SigningFieldModel[];
}

/**
 * Firestore model for Signer (embedded in SigningRequestModel)
 */
export interface SignerModel {
  name: string;
  email: string;
  status: SignerStatus;
  token: string;
}

/**
 * Firestore document: /organizations/{orgId}/signingRequests/{requestId}
 */
export interface SigningRequestModel {
  id: string;
  organizationId: string;
  title: string;
  description?: string;
  meaning: string;
  status: SigningRequestStatus;
  createdBy: string;
  createdByEmail: string;
  createdAt: Timestamp;
  expiresAt: Timestamp;
  signingDocuments: SigningDocumentModel[];
  signers: SignerModel[];
}

/**
 * Firestore subcollection: /signingRequests/{id}/signatures/{signatureId}
 */
export interface SigningRequestSignatureModel {
  id: string;
  signerEmail: string;
  signerName: string;
  signedAt: Timestamp;
  ipAddress: string;
  meaning: string;
  identityVerification: {
    method: 'email_code';
    verifiedAt: Timestamp;
  };
  signatureImagePath: string;
  initialsImagePath: string;
  fieldValues: {
    fieldId: string;
    type: FieldType;
    value: string;
  }[];
}

/**
 * Firestore subcollection: /signingRequests/{id}/auditTrail/{entryId}
 */
export interface SigningRequestAuditEntryModel {
  id: string;
  eventType: SigningRequestAuditEventType;
  actor: string;
  timestamp: Timestamp;
  details?: Record<string, unknown>;
}
```

**Step 2: Create the repository**

Create `packages/data/src/repositories/SigningRequestsRepository.ts`:

```typescript
import { FieldValue, Timestamp } from 'firebase-admin/firestore';
import { getAdminFirestore } from '../adminFirestore.js';
import type {
  SigningRequestModel,
  SigningRequestSignatureModel,
  SigningRequestAuditEntryModel,
  SigningDocumentModel,
  SignerModel,
} from '../models/signingRequestModels.js';
import type { SigningRequestStatus, SignerStatus } from '@pactosigna/shared';

export interface CreateSigningRequestData {
  organizationId: string;
  title: string;
  description?: string;
  meaning: string;
  createdBy: string;
  createdByEmail: string;
  expiresAt: Date;
  signingDocuments: Omit<SigningDocumentModel, 'uploadedAt'>[];
  signers: Omit<SignerModel, 'status'>[];
}

export interface CreateSignatureData {
  signerEmail: string;
  signerName: string;
  ipAddress: string;
  meaning: string;
  identityVerification: {
    method: 'email_code';
    verifiedAt: Date;
  };
  signatureImagePath: string;
  initialsImagePath: string;
  fieldValues: { fieldId: string; type: string; value: string }[];
}

export class SigningRequestsRepository {
  private db = getAdminFirestore();

  private signingRequestsRef(orgId: string) {
    return this.db.collection('organizations').doc(orgId).collection('signingRequests');
  }

  private signaturesRef(orgId: string, requestId: string) {
    return this.signingRequestsRef(orgId).doc(requestId).collection('signatures');
  }

  private auditTrailRef(orgId: string, requestId: string) {
    return this.signingRequestsRef(orgId).doc(requestId).collection('auditTrail');
  }

  async create(data: CreateSigningRequestData): Promise<SigningRequestModel> {
    const docRef = this.signingRequestsRef(data.organizationId).doc();
    const now = FieldValue.serverTimestamp();

    const model: Omit<SigningRequestModel, 'createdAt'> & {
      createdAt: ReturnType<typeof FieldValue.serverTimestamp>;
    } = {
      id: docRef.id,
      organizationId: data.organizationId,
      title: data.title,
      description: data.description,
      meaning: data.meaning,
      status: 'sent',
      createdBy: data.createdBy,
      createdByEmail: data.createdByEmail,
      createdAt: now,
      expiresAt: Timestamp.fromDate(data.expiresAt),
      signingDocuments: data.signingDocuments.map(doc => ({
        ...doc,
        uploadedAt: now as unknown as Timestamp,
      })),
      signers: data.signers.map(s => ({ ...s, status: 'pending' as const })),
    };

    await docRef.set(model);
    return { ...model, createdAt: Timestamp.now() } as SigningRequestModel;
  }

  async findById(orgId: string, requestId: string): Promise<SigningRequestModel | null> {
    const doc = await this.signingRequestsRef(orgId).doc(requestId).get();
    return doc.exists ? (doc.data() as SigningRequestModel) : null;
  }

  async findBySignerToken(orgId: string, token: string): Promise<SigningRequestModel | null> {
    // Query across all signing requests for a signer with this token
    const snapshot = await this.signingRequestsRef(orgId)
      .where('signers', 'array-contains', { token })
      .limit(1)
      .get();

    // Firestore array-contains doesn't work on partial objects.
    // We need to scan — for now, use a collectionGroup query approach.
    // Alternative: store token -> requestId mapping in a separate collection.
    // TODO: Implement efficient token lookup (see Task 12 for token index)
    return snapshot.empty ? null : (snapshot.docs[0].data() as SigningRequestModel);
  }

  async findByOrganization(
    orgId: string,
    filters: { status?: SigningRequestStatus },
    pagination: { limit: number; offset: number }
  ): Promise<{ items: SigningRequestModel[]; total: number }> {
    let query = this.signingRequestsRef(orgId).orderBy('createdAt', 'desc');

    if (filters.status) {
      query = query.where('status', '==', filters.status);
    }

    // Get total count
    const countSnapshot = await query.count().get();
    const total = countSnapshot.data().count;

    // Get paginated results
    const snapshot = await query.offset(pagination.offset).limit(pagination.limit).get();
    const items = snapshot.docs.map(doc => doc.data() as SigningRequestModel);

    return { items, total };
  }

  async updateStatus(
    orgId: string,
    requestId: string,
    status: SigningRequestStatus
  ): Promise<void> {
    await this.signingRequestsRef(orgId).doc(requestId).update({ status });
  }

  async updateSignerStatus(
    orgId: string,
    requestId: string,
    signerEmail: string,
    status: SignerStatus
  ): Promise<void> {
    const doc = await this.findById(orgId, requestId);
    if (!doc) return;

    const updatedSigners = doc.signers.map(s => (s.email === signerEmail ? { ...s, status } : s));
    await this.signingRequestsRef(orgId).doc(requestId).update({ signers: updatedSigners });
  }

  async createSignature(
    orgId: string,
    requestId: string,
    data: CreateSignatureData
  ): Promise<string> {
    const docRef = this.signaturesRef(orgId, requestId).doc();
    const model: Omit<SigningRequestSignatureModel, 'signedAt' | 'identityVerification'> & {
      signedAt: ReturnType<typeof FieldValue.serverTimestamp>;
      identityVerification: { method: 'email_code'; verifiedAt: Timestamp };
    } = {
      id: docRef.id,
      signerEmail: data.signerEmail,
      signerName: data.signerName,
      signedAt: FieldValue.serverTimestamp(),
      ipAddress: data.ipAddress,
      meaning: data.meaning,
      identityVerification: {
        method: data.identityVerification.method,
        verifiedAt: Timestamp.fromDate(data.identityVerification.verifiedAt),
      },
      signatureImagePath: data.signatureImagePath,
      initialsImagePath: data.initialsImagePath,
      fieldValues: data.fieldValues,
    };
    await docRef.set(model);
    return docRef.id;
  }

  async addAuditEntry(
    orgId: string,
    requestId: string,
    entry: Omit<SigningRequestAuditEntryModel, 'id' | 'timestamp'>
  ): Promise<void> {
    const docRef = this.auditTrailRef(orgId, requestId).doc();
    await docRef.set({
      ...entry,
      id: docRef.id,
      timestamp: FieldValue.serverTimestamp(),
    });
  }

  async getAuditTrail(orgId: string, requestId: string): Promise<SigningRequestAuditEntryModel[]> {
    const snapshot = await this.auditTrailRef(orgId, requestId).orderBy('timestamp', 'asc').get();
    return snapshot.docs.map(doc => doc.data() as SigningRequestAuditEntryModel);
  }

  async findExpiredRequests(orgId: string): Promise<SigningRequestModel[]> {
    const now = Timestamp.now();
    const snapshot = await this.signingRequestsRef(orgId)
      .where('status', 'in', ['sent', 'partially_signed'])
      .where('expiresAt', '<', now)
      .get();
    return snapshot.docs.map(doc => doc.data() as SigningRequestModel);
  }
}
```

**Step 3: Export from models and repositories indexes**

Add to `packages/data/src/models/index.ts`:

```typescript
export * from './signingRequestModels.js';
```

Add to `packages/data/src/repositories/index.ts`:

```typescript
export * from './SigningRequestsRepository.js';
```

**Step 4: Run typecheck**

Run: `pnpm --filter @pactosigna/data typecheck`

Expected: PASS

**Step 5: Commit**

```bash
git add packages/data/src/models/signingRequestModels.ts packages/data/src/models/index.ts \
  packages/data/src/repositories/SigningRequestsRepository.ts packages/data/src/repositories/index.ts
git commit -m "feat: add SigningRequest Firestore models and repository"
```

---

### Task 9: Add signing request audit actions

**Files:**

- Modify: `packages/data/src/repositories/AuditRepository.ts`

**Step 1: Add signing request audit actions**

Add to the `AuditAction` type union in `AuditRepository.ts`:

```typescript
  // Signing Requests
  | 'signing_request.created'
  | 'signing_request.sent'
  | 'signing_request.viewed'
  | 'signing_request.signed'
  | 'signing_request.declined'
  | 'signing_request.cancelled'
  | 'signing_request.expired'
  | 'signing_request.reminded';
```

Add to the `ResourceType` union:

```typescript
  | 'signing_request';
```

**Step 2: Run typecheck**

Run: `pnpm --filter @pactosigna/data typecheck`

Expected: PASS

**Step 3: Commit**

```bash
git add packages/data/src/repositories/AuditRepository.ts
git commit -m "feat: add signing request audit actions and resource type"
```

---

### Task 10: Create SigningRequests service

**Files:**

- Create: `apps/services/src/modules/signing-requests/SigningRequestsService.ts`
- Create: `apps/services/src/modules/signing-requests/SigningRequestsService.test.ts`

**Step 1: Write the test file first**

Create `apps/services/src/modules/signing-requests/SigningRequestsService.test.ts` with tests for:

- `createSigningRequest()` — happy path: creates request, adds audit entry, returns response
- `createSigningRequest()` — validates expiry is within 30 days
- `createSigningRequest()` — validates every signer has at least one field
- `getSigningRequest()` — returns detail response with audit trail
- `listSigningRequests()` — returns paginated summary list
- `cancelSigningRequest()` — updates status, adds audit entry
- `remindSigners()` — only reminds pending signers

Follow the same mock pattern as `ReleasesService.test.ts`:

- Mock all repositories with `vi.fn()`
- Construct service with mocks in `beforeEach`
- Test both happy path and error cases

**Step 2: Run tests to verify they fail**

Run: `pnpm --filter @pactosigna/services test -- --testPathPattern signing-requests`

Expected: FAIL (service doesn't exist yet)

**Step 3: Write the service**

Create `apps/services/src/modules/signing-requests/SigningRequestsService.ts`:

The service should implement:

- `createSigningRequest(userId, body)` — verify membership, validate inputs, generate signer tokens, create request, add audit entry, return response
- `getSigningRequest(userId, orgId, requestId)` — verify membership, fetch request + audit trail, return detail response
- `listSigningRequests(userId, orgId, filters, pagination)` — verify membership, query, return summary list
- `cancelSigningRequest(userId, orgId, requestId)` — verify membership, check status is cancellable, update status, add audit entry
- `remindSigners(userId, orgId, requestId)` — verify membership, check request is active, TODO: queue reminder emails

Constructor: `constructor(signingRequestsRepo, orgRepo, auditRepo)`

Use `crypto.randomUUID()` for signer tokens.
Validate `expiresAt` is at most 30 days from now.
Validate every signer email appears in at least one field's `assignedSignerEmail`.

**Step 4: Run tests to verify they pass**

Run: `pnpm --filter @pactosigna/services test -- --testPathPattern signing-requests`

Expected: PASS

**Step 5: Commit**

```bash
git add apps/services/src/modules/signing-requests/
git commit -m "feat: add SigningRequestsService with unit tests"
```

---

### Task 11: Create SigningRequests controller and register

**Files:**

- Create: `apps/services/src/modules/signing-requests/SigningRequestsController.ts`
- Create: `apps/services/src/modules/signing-requests/index.ts`
- Modify: `apps/services/src/app.ts` (register controller)

**Step 1: Create the controller**

Create `apps/services/src/modules/signing-requests/SigningRequestsController.ts`:

Routes (all prefixed with `/api/v2` via registration):

```
GET    /signing-requests                       — list (authenticated)
POST   /signing-requests                       — create (authenticated)
GET    /signing-requests/:signingRequestId      — get detail (authenticated)
POST   /signing-requests/:signingRequestId/cancel   — cancel (authenticated)
POST   /signing-requests/:signingRequestId/remind   — remind (authenticated)
```

External signer routes (NO authentication):

```
GET    /signing-ceremony/:token                — get ceremony data
POST   /signing-ceremony/:token/verify         — request email verification code
POST   /signing-ceremony/:token/complete       — complete signing
```

Follow the same pattern as `ReleasesController.ts`:

- Export as `async function signingRequestsController(app: FastifyInstance)`
- Instantiate repos and service at top level
- Use Zod schemas for validation
- Use `app.authenticate` preHandler for internal routes
- NO preHandler for external signer routes

**Step 2: Create index**

Create `apps/services/src/modules/signing-requests/index.ts`:

```typescript
export { signingRequestsController } from './SigningRequestsController.js';
export { SigningRequestsService } from './SigningRequestsService.js';
```

**Step 3: Register in app.ts**

Add to `apps/services/src/app.ts`:

```typescript
import { signingRequestsController } from './modules/signing-requests/index.js';
// ...
app.register(signingRequestsController, { prefix: '/api/v2' });
```

**Step 4: Run typecheck and tests**

Run: `pnpm --filter @pactosigna/services typecheck`
Run: `pnpm --filter @pactosigna/services test`

Expected: PASS

**Step 5: Commit**

```bash
git add apps/services/src/modules/signing-requests/ apps/services/src/app.ts
git commit -m "feat: add SigningRequests API controller and routes"
```

---

### Task 12: Create signer token index for efficient lookup

**Files:**

- Create: `packages/data/src/repositories/SignerTokensRepository.ts`
- Modify: `packages/data/src/repositories/index.ts`
- Modify: `packages/data/src/repositories/SigningRequestsRepository.ts` (update create to use token index)

Firestore `array-contains` doesn't support partial object matching, so we need a separate collection for token lookups.

**Step 1: Create the token index repository**

Create `packages/data/src/repositories/SignerTokensRepository.ts`:

```typescript
import { getAdminFirestore } from '../adminFirestore.js';

/**
 * Token → SigningRequest mapping for efficient lookup.
 *
 * Firestore path: /signerTokens/{token}
 * This is a root-level collection (not org-scoped) because external signers
 * don't know which organization sent the request.
 */
export class SignerTokensRepository {
  private db = getAdminFirestore();

  private tokensRef() {
    return this.db.collection('signerTokens');
  }

  async create(
    token: string,
    orgId: string,
    requestId: string,
    signerEmail: string
  ): Promise<void> {
    await this.tokensRef().doc(token).set({
      organizationId: orgId,
      signingRequestId: requestId,
      signerEmail,
      createdAt: new Date(),
    });
  }

  async lookup(token: string): Promise<{
    organizationId: string;
    signingRequestId: string;
    signerEmail: string;
  } | null> {
    const doc = await this.tokensRef().doc(token).get();
    if (!doc.exists) return null;
    const data = doc.data()!;
    return {
      organizationId: data.organizationId,
      signingRequestId: data.signingRequestId,
      signerEmail: data.signerEmail,
    };
  }

  async delete(token: string): Promise<void> {
    await this.tokensRef().doc(token).delete();
  }
}
```

**Step 2: Export from index**

Add to `packages/data/src/repositories/index.ts`:

```typescript
export * from './SignerTokensRepository.js';
```

**Step 3: Update SigningRequestsRepository.create() to also create token entries**

This will be handled in the service layer (Task 10) by calling both repositories.

**Step 4: Run typecheck**

Run: `pnpm --filter @pactosigna/data typecheck`

Expected: PASS

**Step 5: Commit**

```bash
git add packages/data/src/repositories/SignerTokensRepository.ts packages/data/src/repositories/index.ts
git commit -m "feat: add SignerTokens repository for efficient token lookup"
```

---

### Task 13: Create frontend API client

**Files:**

- Create: `apps/web/src/shared/services/api/signing-requests-api.ts`

**Step 1: Create the API client**

Create `apps/web/src/shared/services/api/signing-requests-api.ts`:

```typescript
import type {
  ListSigningRequestsResponse,
  SigningRequestDetailResponse,
  SigningRequestSummaryResponse,
  CreateSigningRequestBody,
} from '@pactosigna/contracts';
import { apiFetch, buildUrl } from './base';

// === Types ===

interface ListParams {
  organizationId: string;
  status?: string;
  limit?: number;
  offset?: number;
}

interface GetParams {
  organizationId: string;
  signingRequestId: string;
}

interface CreateParams {
  body: CreateSigningRequestBody;
}

interface ActionParams {
  organizationId: string;
  signingRequestId: string;
}

// === API Functions ===

export async function listSigningRequests(params: ListParams) {
  const url = buildUrl('/api/v2/signing-requests', {
    organizationId: params.organizationId,
    status: params.status,
    limit: params.limit,
    offset: params.offset,
  });
  return apiFetch<ListSigningRequestsResponse>(url, { method: 'GET' });
}

export async function getSigningRequest(params: GetParams) {
  const url = buildUrl(`/api/v2/signing-requests/${params.signingRequestId}`, {
    organizationId: params.organizationId,
  });
  return apiFetch<SigningRequestDetailResponse>(url, { method: 'GET' });
}

export async function createSigningRequest(params: CreateParams) {
  return apiFetch<SigningRequestSummaryResponse>('/api/v2/signing-requests', {
    method: 'POST',
    body: params.body,
  });
}

export async function cancelSigningRequest(params: ActionParams) {
  return apiFetch<void>(`/api/v2/signing-requests/${params.signingRequestId}/cancel`, {
    method: 'POST',
    body: { organizationId: params.organizationId },
  });
}

export async function remindSigners(params: ActionParams) {
  return apiFetch<void>(`/api/v2/signing-requests/${params.signingRequestId}/remind`, {
    method: 'POST',
    body: { organizationId: params.organizationId },
  });
}
```

**Step 2: Commit**

```bash
git add apps/web/src/shared/services/api/signing-requests-api.ts
git commit -m "feat: add signing requests API client"
```

---

### Task 14: Add Signing Requests to sidebar and routes

**Files:**

- Modify: `apps/web/src/shared/layouts/DashboardLayout.tsx` (add nav item for QMS)
- Modify: `apps/web/src/routes/index.tsx` (add routes)
- Create: `apps/web/src/features/signing-requests/pages/SigningRequestsPage.tsx` (placeholder)
- Create: `apps/web/src/features/signing-requests/pages/SigningRequestDetailPage.tsx` (placeholder)
- Create: `apps/web/src/features/signing-requests/pages/NewSigningRequestPage.tsx` (placeholder)

**Step 1: Add sidebar nav item**

In `DashboardLayout.tsx`, add to the QMS nav items array (after Change Controls, before Audit Log):

```typescript
import DrawIcon from '@mui/icons-material/Draw';
// ... in getNavItems, qms section:
{ label: 'Signing Requests', icon: DrawIcon, path: '/qms/signing-requests' },
```

**Step 2: Create placeholder pages**

Create minimal page components that render a title and "Coming soon" text. Each must have a `default` export for lazy loading.

`apps/web/src/features/signing-requests/pages/SigningRequestsPage.tsx`:

```tsx
import Typography from '@mui/material/Typography';

export default function SigningRequestsPage() {
  return <Typography variant="h4">Signing Requests</Typography>;
}
```

Similarly for `SigningRequestDetailPage.tsx` and `NewSigningRequestPage.tsx`.

**Step 3: Add routes**

In `apps/web/src/routes/index.tsx`, add lazy imports and routes under QMS section:

```tsx
const SigningRequestsPage = lazy(() => import('@/features/signing-requests/pages/SigningRequestsPage'));
const SigningRequestDetailPage = lazy(() => import('@/features/signing-requests/pages/SigningRequestDetailPage'));
const NewSigningRequestPage = lazy(() => import('@/features/signing-requests/pages/NewSigningRequestPage'));

// In QMS routes section:
<Route path="/qms/signing-requests" element={<LazyPage><SigningRequestsPage /></LazyPage>} />
<Route path="/qms/signing-requests/new" element={<LazyPage><NewSigningRequestPage /></LazyPage>} />
<Route path="/qms/signing-requests/:signingRequestId" element={<LazyPage><SigningRequestDetailPage /></LazyPage>} />
```

Also add the external signing route OUTSIDE the protected route (unauthenticated):

```tsx
{
  /* External signing - no auth required */
}
<Route
  path="/sign/:token"
  element={
    <LazyPage>
      <ExternalSigningPage />
    </LazyPage>
  }
/>;
```

**Step 4: Run typecheck**

Run: `pnpm --filter @pactosigna/web typecheck`

Expected: PASS

**Step 5: Commit**

```bash
git add apps/web/src/shared/layouts/DashboardLayout.tsx apps/web/src/routes/index.tsx \
  apps/web/src/features/signing-requests/
git commit -m "feat: add signing requests routes, sidebar nav, and placeholder pages"
```

---

### Task 15: Install PDF and signature dependencies

**Files:**

- Modify: `apps/web/package.json` (add frontend deps)
- Modify: `apps/functions/package.json` (add pdf-lib)

**Step 1: Install frontend dependencies**

```bash
cd /workspaces/PactoSigna.Experience.GitWeb
pnpm --filter @pactosigna/web add @react-pdf-viewer/core @react-pdf-viewer/default-layout pdfjs-dist react-signature-canvas
pnpm --filter @pactosigna/web add -D @types/react-signature-canvas
```

**Step 2: Install backend dependency**

```bash
pnpm --filter @pactosigna/functions add pdf-lib
```

**Step 3: Verify installation**

Run: `pnpm install`
Run: `pnpm --filter @pactosigna/web typecheck`

Expected: PASS

**Step 4: Commit**

```bash
git add apps/web/package.json apps/functions/package.json pnpm-lock.yaml
git commit -m "deps: add PDF viewer, signature canvas, and pdf-lib"
```

---

### Task 16: Build Signing Requests list page

**Files:**

- Create: `apps/web/src/features/signing-requests/hooks/useSigningRequests.ts`
- Modify: `apps/web/src/features/signing-requests/pages/SigningRequestsPage.tsx`

**Step 1: Create the ViewModel hook**

Create `apps/web/src/features/signing-requests/hooks/useSigningRequests.ts`:

Uses `useQuery` from React Query with `listSigningRequests` API call. Returns `{ data, isLoading, error }`. Gets organization from `useOrganization()`.

**Step 2: Build the list page**

Replace the placeholder `SigningRequestsPage.tsx` with a full implementation:

- Table with columns: Title, Status (chip), Signers progress, Created, Expires
- Status chips with colors per terminology doc
- "New Signing Request" button linking to `/qms/signing-requests/new`
- Empty state per UX spec
- Click row navigates to detail page

**Step 3: Run typecheck**

Run: `pnpm --filter @pactosigna/web typecheck`

Expected: PASS

**Step 4: Commit**

```bash
git add apps/web/src/features/signing-requests/
git commit -m "feat: build Signing Requests list page with status chips"
```

---

### Task 17: Build Signing Request detail page

**Files:**

- Create: `apps/web/src/features/signing-requests/hooks/useSigningRequestDetail.ts`
- Modify: `apps/web/src/features/signing-requests/pages/SigningRequestDetailPage.tsx`

**Step 1: Create the ViewModel hook**

Uses `useQuery` for detail data and `useMutation` for cancel/remind actions. Returns `{ data, isLoading, cancel, remind }`.

**Step 2: Build the detail page**

Replace placeholder with:

- Request metadata section (title, description, meaning, dates, created by)
- Documents section (file names, hashes, download links)
- Signers table (name, email, status chip, signed-at)
- Audit trail timeline
- Action buttons: Remind, Cancel, Download Signed PDF (when completed)

**Step 3: Run typecheck**

Run: `pnpm --filter @pactosigna/web typecheck`

Expected: PASS

**Step 4: Commit**

```bash
git add apps/web/src/features/signing-requests/
git commit -m "feat: build Signing Request detail page"
```

---

### Task 18: Build PDF Field Placement Editor component

**Files:**

- Create: `apps/web/src/features/signing-requests/components/FieldPlacementEditor.tsx`
- Create: `apps/web/src/features/signing-requests/components/FieldOverlay.tsx`

This is the most complex frontend component. The editor renders a PDF page-by-page and allows the sender to drag field markers onto the pages.

**Step 1: Build FieldOverlay**

A single positioned field on the PDF. Rendered as an absolutely-positioned `<div>` over the PDF canvas. Draggable and resizable. Shows signer name and field type label. Color-coded by signer.

**Step 2: Build FieldPlacementEditor**

Uses `@react-pdf-viewer/core` to render PDF pages. Accepts `signers` and `fields` as props plus `onFieldsChange` callback. Features:

- Left sidebar with field palette (Signature, Initials, Date buttons)
- Each field type has a dropdown to assign to a signer
- Drag from palette onto PDF page to create a field
- Page navigation (prev/next, page number indicator)
- Zoom controls
- Fields stored as percentage coordinates

**Step 3: Run typecheck**

Run: `pnpm --filter @pactosigna/web typecheck`

Expected: PASS

**Step 4: Commit**

```bash
git add apps/web/src/features/signing-requests/components/
git commit -m "feat: build PDF Field Placement Editor component"
```

---

### Task 19: Build New Signing Request page

**Files:**

- Modify: `apps/web/src/features/signing-requests/pages/NewSigningRequestPage.tsx`
- Create: `apps/web/src/features/signing-requests/hooks/useCreateSigningRequest.ts`

**Step 1: Create the mutation hook**

Uses `useMutation` with `createSigningRequest` API call. Invalidates the list query on success. Navigates to the detail page on success.

**Step 2: Build the creation form**

Multi-step form:

1. **Basic info**: Title, Description, Meaning (dropdown + custom), Expiry date
2. **Upload PDFs**: Drag-and-drop zone for PDF files. List uploaded files with remove button. Upload to Cloud Storage and capture storagePath + fileHash + pageCount.
3. **Add signers**: Name + email input pairs. Add/remove signer buttons.
4. **Place fields**: For each document, open the FieldPlacementEditor. Assign fields to signers.
5. **Review & Send**: Summary of everything. "Send" button with validation.

Validation before send:

- At least one document
- At least one signer
- Every signer has at least one field
- Every field has a signer

**Step 3: Run typecheck**

Run: `pnpm --filter @pactosigna/web typecheck`

Expected: PASS

**Step 4: Commit**

```bash
git add apps/web/src/features/signing-requests/
git commit -m "feat: build New Signing Request page with field placement"
```

---

### Task 20: Build Signature Capture component

**Files:**

- Create: `apps/web/src/features/signing-requests/components/SignatureCapture.tsx`

**Step 1: Build the component**

Modal/dialog with two tabs:

- **Draw tab**: `react-signature-canvas` for freehand drawing. Clear button. Reasonable canvas size.
- **Type tab**: Text input that renders in a signature-style font (e.g., `Caveat` or `Dancing Script` from Google Fonts). Preview of rendered text.

Props: `{ mode: 'signature' | 'initials', onCapture: (dataUrl: string) => void, onCancel: () => void }`

Returns a data URL (base64 PNG) of the captured signature/initials.

**Step 2: Run typecheck**

Run: `pnpm --filter @pactosigna/web typecheck`

Expected: PASS

**Step 3: Commit**

```bash
git add apps/web/src/features/signing-requests/components/SignatureCapture.tsx
git commit -m "feat: build SignatureCapture component with draw and type modes"
```

---

### Task 21: Build External Signing Page

**Files:**

- Create: `apps/web/src/features/signing-requests/pages/ExternalSigningPage.tsx`
- Create: `apps/web/src/features/signing-requests/hooks/useSigningCeremony.ts`

**Step 1: Create the ceremony hook**

Fetches ceremony data from `GET /api/v2/signing-ceremony/:token` (unauthenticated). Provides mutations for verify code and complete signing.

Note: This API call does NOT use `apiFetch` (which requires auth). Create a separate `publicFetch` helper or inline fetch in the hook.

**Step 2: Build the external signing page**

Standalone page (outside DashboardLayout, no sidebar):

- PactoSigna logo at top
- "[Organization] has requested your signature" header
- Request title and description
- Embedded PDF viewer showing the current document with this signer's fields highlighted
- Click a field → opens SignatureCapture modal (signature or initials) or shows auto-date
- Fields turn green when filled
- Document navigation (1 of N)
- Summary step with meaning text, email verification, and legal notice
- "Complete Signing" button
- Error states: expired, cancelled, already signed

**Step 3: Add route for this page**

Already added in Task 14 as `/sign/:token` outside the ProtectedRoute.

**Step 4: Run typecheck**

Run: `pnpm --filter @pactosigna/web typecheck`

Expected: PASS

**Step 5: Commit**

```bash
git add apps/web/src/features/signing-requests/
git commit -m "feat: build External Signing Page with embedded PDF and field filling"
```

---

### Task 22: Build email notification for signing invitations

**Files:**

- Create: `apps/functions/src/signing-requests/email.ts`
- Modify: `apps/services/src/modules/signing-requests/SigningRequestsService.ts` (call email after create)

**Step 1: Create email templates**

Create `apps/functions/src/signing-requests/email.ts` with functions for:

- `buildSigningInvitationEmail(signerName, organizationName, title, signingUrl)` — returns HTML email body
- `buildVerificationCodeEmail(signerName, code)` — returns HTML with 6-digit code
- `buildSigningConfirmationEmail(signerName, title)` — returns confirmation HTML

Follow the same Microsoft Graph API pattern used in `apps/functions/src/export/email.ts`.

**Step 2: Integrate email sending into the service**

After creating a signing request in the service, queue or send emails to each signer. For v1, send inline (not queued) since the volume is low.

**Step 3: Commit**

```bash
git add apps/functions/src/signing-requests/ apps/services/src/modules/signing-requests/
git commit -m "feat: add email notifications for signing invitations"
```

---

### Task 23: Build signed PDF generation worker

**Files:**

- Create: `apps/functions/src/signing-requests/signedPdfWorker.ts`
- Modify: `apps/functions/src/index.ts` (export worker)
- Modify: `firebase.json` (add rewrite for worker)

**Step 1: Create the worker**

Uses `pdf-lib` to:

1. Load the original PDF from Cloud Storage
2. For each field value, overlay the signature/initials image or date text at the specified coordinates
3. Save the modified PDF to Cloud Storage
4. Update the signing request with the signed PDF path

The worker receives: `{ organizationId, signingRequestId }` and processes all documents.

**Step 2: Export from functions index**

Add to `apps/functions/src/index.ts`:

```typescript
export { signedPdfWorker } from './signing-requests/signedPdfWorker.js';
```

**Step 3: Add firebase.json rewrite**

Add ABOVE the `/api/**` catch-all:

```json
{ "source": "/api/tasks/signing-pdf-worker", "function": "signedPdfWorker" },
```

**Step 4: Commit**

```bash
git add apps/functions/src/signing-requests/ apps/functions/src/index.ts firebase.json
git commit -m "feat: add signed PDF generation worker using pdf-lib"
```

---

### Task 24: Build expiry checker scheduled function

**Files:**

- Create: `apps/functions/src/signing-requests/expiryChecker.ts`
- Modify: `apps/functions/src/index.ts` (export function)

**Step 1: Create the scheduled function**

Uses Firebase scheduled functions (`onSchedule`) to run daily. Queries all organizations for signing requests with status `sent` or `partially_signed` where `expiresAt < now`. Updates status to `expired` and adds audit trail entry.

**Step 2: Export from functions index**

Add to `apps/functions/src/index.ts`:

```typescript
export { signingExpiryChecker } from './signing-requests/expiryChecker.js';
```

**Step 3: Commit**

```bash
git add apps/functions/src/signing-requests/expiryChecker.ts apps/functions/src/index.ts
git commit -m "feat: add signing request expiry checker scheduled function"
```

---

### Task 25: Full integration test and cleanup

**Step 1: Run full typecheck**

Run: `pnpm typecheck`

Expected: PASS — zero errors across the entire monorepo.

**Step 2: Run all unit tests**

Run: `pnpm test`

Expected: PASS

**Step 3: Run lint and format**

Run: `pnpm lint:fix && pnpm format`

Fix any issues.

**Step 4: Run code simplifier**

Use the `code-simplifier` agent on all new files in:

- `packages/shared/src/types/signing-request.ts`
- `packages/contracts/src/signing-requests/`
- `packages/data/src/repositories/SigningRequestsRepository.ts`
- `packages/data/src/repositories/SignerTokensRepository.ts`
- `apps/services/src/modules/signing-requests/`
- `apps/web/src/features/signing-requests/`

**Step 5: Run tests again after simplification**

Run: `pnpm test`

Expected: PASS

**Step 6: Final commit**

```bash
git add -A
git commit -m "chore: lint, format, and simplify signing requests code"
```

---

## Summary

| Phase                  | Tasks       | What's built                                                                                                 |
| ---------------------- | ----------- | ------------------------------------------------------------------------------------------------------------ |
| **Phase 1**            | Tasks 1-5   | Auditor role removed from entire codebase                                                                    |
| **Phase 2a: Backend**  | Tasks 6-12  | Types, contracts, models, repository, service, controller, token index                                       |
| **Phase 2b: Frontend** | Tasks 13-21 | API client, routes, list page, detail page, field placement editor, signature capture, external signing page |
| **Phase 2c: Workers**  | Tasks 22-24 | Email notifications, signed PDF generation, expiry checker                                                   |
| **Phase 2d: Cleanup**  | Task 25     | Integration test, lint, format, simplify                                                                     |
