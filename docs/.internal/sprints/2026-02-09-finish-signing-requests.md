# Plan: Finish Signing Requests Implementation

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Complete the remaining ~15-20% of the Signing Request feature — primarily the external signing ceremony (backend + frontend), PDF upload, field placement editor, signature capture, and email integration.

**Reference:** Original plan in `docs/plans/2026-02-07-signing-requests.md`, UX spec in `docs/ux/external-signing/`.

---

## Inventory: What's Already Built

### Backend (Complete)

- Domain types: `packages/shared/src/types/signing-request.ts`
- Contracts (Zod): `packages/contracts/src/signing-requests/`
- Firestore models: `packages/data/src/models/signingRequestModels.ts`
- Repository: `packages/data/src/repositories/SigningRequestsRepository.ts`
- Token index: `packages/data/src/repositories/SignerTokensRepository.ts`
- Service (CRUD): `apps/services/src/modules/signing-requests/SigningRequestsService.ts`
- Controller: `apps/services/src/modules/signing-requests/SigningRequestsController.ts`
- Query routes: `signingRequestsQueryRoutes.ts` (GET list, GET detail — authenticated)
- Mutation routes: `signingRequestsMutationRoutes.ts` (POST create, POST cancel, POST remind — authenticated)
- Unit tests: `SigningRequestsService.test.ts` (7 tests passing)
- Audit actions registered

### Cloud Functions (Complete)

- Notification worker: `apps/functions/src/signing-requests/notificationWorker.ts` + `processNotification.ts` + `email.ts` + `sendSigningEmail.ts`
- Signed PDF worker: `apps/functions/src/signing-requests/signedPdfWorker.ts` + `processSignedPdf.ts`
- Expiry checker: `apps/functions/src/signing-requests/expiryChecker.ts`
- All exported from `apps/functions/src/index.ts`
- Firebase.json rewrites configured

### Frontend (Partially Complete)

- API client: `apps/web/src/shared/services/api/signing-requests-api.ts` (5 functions, all authenticated)
- Routes: All 4 routes registered in `apps/web/src/routes/index.tsx` (list, new, detail, `/sign/:token`)
- Sidebar: "Signing Requests" nav item with DrawIcon
- List page: **Complete** — table with status chips, empty state, navigation
- Detail page: **Complete** — metadata, documents list, signers table, audit trail, cancel/remind actions
- New page: **Scaffolded but uses placeholder documents** — has title, description, meaning, expiry, signers form, but no PDF upload or field placement
- External signing page: **Stub only** — just shows "Loading signing ceremony..."
- Hooks: `useSigningRequests`, `useSigningRequestDetail`, `useCreateSigningRequest` — all complete
- Components: `AuditTrail`, `SignersTable`, `SigningRequestMetadata`, `RequestDetailsForm`, `SignersForm`
- Constants: `STATUS_COLORS`, `STATUS_LABELS`, `SIGNER_STATUS_COLORS`

### NOT Built Yet

1. External signer API routes (ceremony GET, verify POST, complete POST)
2. Signing ceremony service logic (fetch by token, verification codes, complete signing)
3. PDF upload API endpoint + Cloud Storage integration
4. Frontend PDF viewer (`@react-pdf-viewer/core` not installed)
5. Frontend signature canvas (`react-signature-canvas` not installed)
6. Field Placement Editor component
7. Signature Capture component
8. External Signing Page (full implementation)
9. Public (unauthenticated) API client for external signing
10. Email sending integration in `remindSigners()` (TODO on line 246)
11. Email sending after `createSigningRequest()` (invitation emails)
12. Download signed PDF button on detail page

---

## Implementation Plan

### Phase A: Install Dependencies (Task 1)

### Task 1: Install frontend PDF and signature dependencies

**Files:**

- Modify: `apps/web/package.json`

**Steps:**

1. Install frontend dependencies:

```bash
pnpm --filter @pactosigna/web add @react-pdf-viewer/core @react-pdf-viewer/default-layout pdfjs-dist react-signature-canvas
pnpm --filter @pactosigna/web add -D @types/react-signature-canvas
```

2. Verify installation:

```bash
pnpm install
pnpm --filter @pactosigna/web typecheck
```

3. Commit:

```bash
git add apps/web/package.json pnpm-lock.yaml
git commit -m "deps: add PDF viewer, signature canvas for signing requests"
```

---

### Phase B: Backend — External Signing Ceremony (Tasks 2-4)

### Task 2: Add signing ceremony service methods

**Files:**

- Modify: `apps/services/src/modules/signing-requests/SigningRequestsService.ts`
- Modify: `apps/services/src/modules/signing-requests/SigningRequestsService.test.ts`

The service needs 4 new methods for external signer operations:

**Step 1: Write tests first**

Add test cases:

- `getSigningCeremony(token)` — happy path: returns ceremony data (org name, title, description, meaning, signer info, documents with only this signer's fields, status: ready/already_signed/expired/cancelled)
- `getSigningCeremony(token)` — invalid token returns NOT_FOUND
- `getSigningCeremony(token)` — expired request returns status 'expired'
- `getSigningCeremony(token)` — already-signed signer returns status 'already_signed'
- `requestVerificationCode(token)` — generates 6-digit code, stores in memory/Firestore, calls notification worker
- `requestVerificationCode(token)` — rejects if request is expired/cancelled
- `completeSigningCeremony(token, code, signatureData)` — validates code, creates signature record, updates signer status, updates request status if all signed, creates audit entries
- `completeSigningCeremony(token, code, signatureData)` — rejects invalid verification code
- `completeSigningCeremony(token, code, signatureData)` — rejects if signer already signed
- `completeSigningCeremony(token, code, signatureData)` — moves request to 'completed' when last signer signs
- `completeSigningCeremony(token, code, signatureData)` — moves request to 'partially_signed' when not all signed yet

**Step 2: Implement the methods**

```typescript
async getSigningCeremony(token: string): Promise<SigningCeremonyResponse>
```

- Lookup token via `signerTokensRepo.lookup(token)`
- Fetch signing request via `signingRequestsRepo.findById(orgId, requestId)`
- Fetch org name from `orgRepo.findById(orgId)`
- Determine ceremony status: 'ready', 'already_signed', 'expired', 'cancelled'
- Filter fields to only those assigned to this signer's email
- Update signer status to 'viewed' if currently 'pending' (and add audit entry)
- Return ceremony response

```typescript
async requestVerificationCode(token: string): Promise<void>
```

- Lookup token, validate request is active
- Generate 6-digit code, store in Firestore `verificationCodes` collection with 10-min TTL
- Call notification worker HTTP endpoint (or send email inline for v1) with type: 'verification_code'

```typescript
async completeSigningCeremony(token: string, verificationCode: string, signatureData: CompleteSigningData): Promise<void>
```

- Lookup token, validate request is active, validate signer hasn't already signed
- Validate verification code (check Firestore, check TTL, check matches)
- Create signature record in subcollection with all field values
- Update signer status to 'signed'
- Check if all signers have signed → update request status to 'completed' or 'partially_signed'
- Add audit entries (viewed, signed)
- If completed: trigger signed PDF generation (queue or inline call to signedPdfWorker)
- Delete verification code from Firestore
- Add org-level audit log entry

**Step 3: Add VerificationCode Firestore model**

Add to `packages/data/src/models/signingRequestModels.ts`:

```typescript
export interface VerificationCodeModel {
  code: string;
  signerEmail: string;
  signingRequestId: string;
  organizationId: string;
  expiresAt: Timestamp;
}
```

Add repository or inline methods in `SigningRequestsRepository` to store/validate codes. Use a root-level `verificationCodes/{token}` collection for simplicity — keyed by signer token so each signer can only have one active code.

**Step 4: Run tests**

```bash
pnpm --filter @pactosigna/services test -- --testPathPattern signing-requests
```

**Step 5: Commit**

```bash
git add apps/services/src/modules/signing-requests/ packages/data/src/
git commit -m "feat: add signing ceremony service methods with verification codes"
```

---

### Task 3: Add signing ceremony API routes (unauthenticated)

**Files:**

- Create: `apps/services/src/modules/signing-requests/signingCeremonyRoutes.ts`
- Modify: `apps/services/src/modules/signing-requests/SigningRequestsController.ts`

**Step 1: Create ceremony routes**

These routes are **unauthenticated** (no `preHandler: [app.authenticate]`):

```
GET    /signing-ceremony/:token           → getSigningCeremony(token)
POST   /signing-ceremony/:token/verify    → requestVerificationCode(token)
POST   /signing-ceremony/:token/complete  → completeSigningCeremony(token, body)
```

Use existing Zod schemas from contracts: `GetSigningCeremonyParamSchema`, `RequestVerificationCodeSchema`, `CompleteSigningCeremonySchema`.

For the complete route, capture `req.ip` for the signature record.

**Step 2: Register in controller**

Add `signingCeremonyRoutes(app, service)` call in `SigningRequestsController.ts`.

**Step 3: Run typecheck and tests**

```bash
pnpm --filter @pactosigna/services typecheck
pnpm --filter @pactosigna/services test
```

**Step 4: Commit**

```bash
git add apps/services/src/modules/signing-requests/
git commit -m "feat: add unauthenticated signing ceremony API routes"
```

---

### Task 4: Add PDF upload endpoint

**Files:**

- Create: `apps/services/src/modules/signing-requests/signingUploadRoutes.ts`
- Modify: `apps/services/src/modules/signing-requests/SigningRequestsController.ts`
- May need: `@fastify/multipart` dependency in `apps/services/package.json`

This endpoint handles PDF file uploads from the New Signing Request form.

**Step 1: Check if `@fastify/multipart` is already installed**

```bash
grep -r "multipart" apps/services/package.json
```

If not installed:

```bash
pnpm --filter @pactosigna/services add @fastify/multipart
```

**Step 2: Create upload route**

```
POST /signing-requests/upload — authenticated, multipart form data
```

The route should:

1. Accept a PDF file via multipart upload
2. Validate file type (application/pdf) and size (max 25MB)
3. Compute SHA-256 hash of the file
4. Upload to Cloud Storage: `organizations/{orgId}/signing-uploads/{uuid}.pdf`
5. Count pages using pdf-lib (load PDF, get page count)
6. Return: `{ storagePath, fileHash, pageCount, fileName }`

**Step 3: Register in controller**

**Step 4: Run typecheck**

```bash
pnpm --filter @pactosigna/services typecheck
```

**Step 5: Commit**

```bash
git add apps/services/src/modules/signing-requests/ apps/services/package.json pnpm-lock.yaml
git commit -m "feat: add PDF upload endpoint for signing requests"
```

---

### Phase C: Frontend — Creation Flow (Tasks 5-7)

### Task 5: Build PDF upload and Field Placement Editor components

**Files:**

- Create: `apps/web/src/features/signing-requests/components/PdfUploader.tsx`
- Create: `apps/web/src/features/signing-requests/components/FieldPlacementEditor.tsx`
- Create: `apps/web/src/features/signing-requests/components/FieldOverlay.tsx`

**PdfUploader** (Step 1):

- Drag-and-drop zone for PDF files (use MUI Paper with dashed border)
- Calls the upload endpoint (`POST /signing-requests/upload`)
- Shows upload progress
- Displays uploaded files in a list with: file name, page count, remove button
- Returns array of uploaded document metadata to parent

**FieldOverlay** (Step 2):

- A single positioned field rendered as an absolutely-positioned `<div>` over the PDF page
- Shows signer name and field type icon/label
- Color-coded by signer (use MUI palette colors)
- Draggable within the page container (mouse events → update x/y percentages)
- Delete button on hover
- Props: `{ field, pageWidth, pageHeight, signerColor, onMove, onDelete }`

**FieldPlacementEditor** (Step 3):

- Uses `@react-pdf-viewer/core` to render one PDF at a time, page by page
- Accepts `signers`, `uploadedDocument`, `fields`, `onFieldsChange` as props
- Left sidebar with field palette: Signature, Initials, Date buttons
- Each palette button has a signer dropdown (which signer to assign the field to)
- Click a palette button → enters "placement mode" → click on PDF page to place a field at that location
- Renders `FieldOverlay` components for each placed field on the current page
- Page navigation (prev/next, page indicator)
- Zoom controls (fit width, fit page, zoom in/out)
- Fields stored as percentage coordinates (x, y as % of page dimensions)
- Default field sizes: Signature 30%w x 10%h, Initials 10%w x 5%h, Date 15%w x 5%h

**Step 4: Run typecheck**

```bash
pnpm --filter @pactosigna/web typecheck
```

**Step 5: Commit**

```bash
git add apps/web/src/features/signing-requests/components/
git commit -m "feat: build PDF upload and Field Placement Editor components"
```

---

### Task 6: Build Signature Capture component

**Files:**

- Create: `apps/web/src/features/signing-requests/components/SignatureCapture.tsx`

A modal/dialog for capturing signature or initials:

**Draw tab:**

- Uses `react-signature-canvas` for freehand drawing
- Clear button to reset
- Reasonable canvas size (~400x200 for signature, ~200x100 for initials)

**Type tab:**

- Text input that renders in a signature-style font
- Use a web-safe cursive font or import a Google Font like `Caveat` or `Dancing Script`
- Preview of rendered text in a canvas/styled div

**Props:**

```typescript
interface SignatureCaptureProps {
  mode: 'signature' | 'initials';
  open: boolean;
  onCapture: (dataUrl: string) => void;
  onCancel: () => void;
}
```

Returns a data URL (base64 PNG) of the captured image.

**Step 2: Run typecheck**

```bash
pnpm --filter @pactosigna/web typecheck
```

**Step 3: Commit**

```bash
git add apps/web/src/features/signing-requests/components/SignatureCapture.tsx
git commit -m "feat: build SignatureCapture component with draw and type modes"
```

---

### Task 7: Upgrade NewSigningRequestPage with real PDF upload and field placement

**Files:**

- Modify: `apps/web/src/features/signing-requests/pages/NewSigningRequestPage.tsx`
- Modify: `apps/web/src/features/signing-requests/hooks/useCreateSigningRequest.ts` (if needed)

Replace the `DocumentPlaceholder` and `buildSigningDocuments` stub with real PDF upload and field placement:

**Step 1: Refactor the page to a multi-step form**

Steps:

1. **Basic Info** — Title, Description, Meaning (existing `RequestDetailsForm`), Expiry days
2. **Signers** — Name + email pairs (existing `SignersForm`)
3. **Documents & Fields** — `PdfUploader` + for each uploaded doc, a `FieldPlacementEditor`
4. **Review & Send** — Summary of everything, validation, "Send" button

Use simple step state (not MUI Stepper unless it simplifies things). Could use tab-like sections on a single page.

**Step 2: Wire up file upload**

- Add an upload API function to `signing-requests-api.ts`:

```typescript
export async function uploadSigningDocument(
  file: File,
  organizationId: string
): Promise<UploadResult>;
```

- `PdfUploader` calls this for each dropped file
- Store results in form state

**Step 3: Wire up field placement**

- For each uploaded document, show a "Place Fields" button
- Opens `FieldPlacementEditor` with the uploaded PDF URL and signer list
- Saves field positions to form state

**Step 4: Update submit logic**

Replace `buildSigningDocuments()` with real data from upload + field placement state. The `CreateSigningRequestBody` already expects the full `signingDocuments` array with fields.

**Step 5: Validation before send**

- At least one document uploaded
- At least one signer
- Every signer has at least one field across all documents
- Every field has a signer assigned

**Step 6: Run typecheck**

```bash
pnpm --filter @pactosigna/web typecheck
```

**Step 7: Commit**

```bash
git add apps/web/src/features/signing-requests/
git commit -m "feat: upgrade NewSigningRequestPage with PDF upload and field placement"
```

---

### Phase D: Frontend — External Signing Ceremony (Tasks 8-9)

### Task 8: Create public API client and signing ceremony hook

**Files:**

- Create: `apps/web/src/shared/services/api/signing-ceremony-api.ts`
- Create: `apps/web/src/features/signing-requests/hooks/useSigningCeremony.ts`

**Step 1: Create public API client**

The existing `apiFetch` requires Firebase auth. External signers are NOT authenticated. Create a separate `publicFetch` helper or standalone functions that use plain `fetch`:

```typescript
// signing-ceremony-api.ts
import type { SigningCeremonyResponse } from '@pactosigna/contracts';

export async function getSigningCeremony(token: string): Promise<SigningCeremonyResponse> {
  const response = await fetch(`/api/v2/signing-ceremony/${token}`);
  if (!response.ok) {
    const error = await response.json().catch(() => ({}));
    throw new Error(error.message || `Request failed: ${response.status}`);
  }
  return response.json();
}

export async function requestVerificationCode(token: string): Promise<void> {
  const response = await fetch(`/api/v2/signing-ceremony/${token}/verify`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ token }),
  });
  if (!response.ok) throw new Error('Failed to send verification code');
}

export async function completeSigningCeremony(
  token: string,
  body: CompleteSigningCeremonyBody
): Promise<void> {
  const response = await fetch(`/api/v2/signing-ceremony/${token}/complete`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(body),
  });
  if (!response.ok) {
    const error = await response.json().catch(() => ({}));
    throw new Error(error.message || 'Signing failed');
  }
}
```

**Step 2: Create ceremony hook**

```typescript
// useSigningCeremony.ts
export function useSigningCeremony(token: string) {
  // useQuery for ceremony data
  // useMutation for requestVerificationCode
  // useMutation for completeSigningCeremony
  // Local state for: captured signature dataUrl, captured initials dataUrl, field values, verification code
  return { ceremony, isLoading, error, ... };
}
```

**Step 3: Commit**

```bash
git add apps/web/src/shared/services/api/signing-ceremony-api.ts \
  apps/web/src/features/signing-requests/hooks/useSigningCeremony.ts
git commit -m "feat: add public signing ceremony API client and hook"
```

---

### Task 9: Build the External Signing Page

**Files:**

- Modify: `apps/web/src/features/signing-requests/pages/ExternalSigningPage.tsx`

Replace the placeholder with the full signing ceremony experience. This is standalone (no DashboardLayout, no sidebar).

**Step 1: Build the page layout**

Top-level structure:

```
┌─────────────────────────────────────────┐
│ PactoSigna Logo                          │
│ "[Organization] has requested your       │
│  signature"                              │
│ Title: "Q1 2026 Audit Report"            │
│ Description (if any)                     │
├─────────────────────────────────────────┤
│ Document 1 of 3   [Prev] [Next]         │
│ ┌─────────────────────────────────────┐ │
│ │                                     │ │
│ │  Embedded PDF with field overlays   │ │
│ │  (highlighted in orange, turn green │ │
│ │   when filled)                      │ │
│ │                                     │ │
│ └─────────────────────────────────────┘ │
├─────────────────────────────────────────┤
│ Progress: 3 of 5 fields completed       │
│                                         │
│ [Complete Signing →]  (disabled until    │
│  all fields filled)                     │
└─────────────────────────────────────────┘
```

**Step 2: Implement states**

The page has several states based on `ceremony.status`:

- `'ready'` → Show the signing interface
- `'already_signed'` → Show "You have already signed this request" with signed date
- `'expired'` → Show "This signing link is no longer valid"
- `'cancelled'` → Show "This signing request has been cancelled"
- Loading → Show spinner
- Error (invalid token) → Show "Invalid signing link"

**Step 3: Implement the signing flow**

1. Render PDF using `@react-pdf-viewer/core` (read-only, no field placement — just viewing)
2. Overlay this signer's fields on the PDF (from `ceremony.fields`)
3. Fields appear as highlighted boxes with labels ("Sign here", "Initial here", "Date")
4. Click a signature field → open `SignatureCapture` modal in 'signature' mode
   - Once captured, the signature image auto-applies to ALL signature fields for this signer
5. Click an initials field → open `SignatureCapture` modal in 'initials' mode
   - Once captured, auto-applies to all initials fields
6. Date fields → auto-populated with today's date (read-only, shown with green check)
7. Filled fields turn green with checkmark
8. Document navigation if multiple documents
9. Track completion: N of M fields filled

**Step 4: Implement the summary/verification step**

When all fields are filled on all documents:

1. Show summary: "By signing, you attest: [meaning text]"
2. Legal notice
3. "Send Verification Code" button → calls `requestVerificationCode(token)`
4. Show verification code input field (6 digits)
5. "Resend Code" link with cooldown
6. "Complete Signing" button → calls `completeSigningCeremony(token, { verificationCode, signatureImagePath, initialsImagePath, fieldValues })`

Note on image storage: The signature/initials data URLs need to be uploaded to Cloud Storage before submitting. Options:

- Upload via a new endpoint `POST /signing-ceremony/:token/upload-signature` → returns storagePath
- Or include the data URL in the complete request body and have the server store it

Simpler approach for v1: Include base64 data URLs directly in the `fieldValues` and have the backend service extract and store them. This avoids a separate upload endpoint for unauthenticated users. Update the `CompleteSigningCeremonySchema` to accept `signatureDataUrl` and `initialsDataUrl` fields.

**Step 5: Confirmation screen**

After successful completion:

- "Thank you. Your signature has been recorded."
- Date and time of signing
- "You will receive a confirmation email shortly"

**Step 6: Run typecheck**

```bash
pnpm --filter @pactosigna/web typecheck
```

**Step 7: Commit**

```bash
git add apps/web/src/features/signing-requests/pages/ExternalSigningPage.tsx
git commit -m "feat: build External Signing Page with PDF viewer and signing ceremony"
```

---

### Phase E: Email Integration (Tasks 10-11)

### Task 10: Integrate invitation emails on request creation

**Files:**

- Modify: `apps/services/src/modules/signing-requests/SigningRequestsService.ts`

After `createSigningRequest()` successfully creates the request and token entries, trigger invitation emails.

**Step 1: Call the notification worker**

At the end of `createSigningRequest()`, after all Firestore writes succeed, call the notification worker:

```typescript
// Option A: Direct HTTP call to notification worker (works in production)
// Option B: Inline email sending (simpler for v1)
```

For v1, make an HTTP POST to the notification worker URL:

```typescript
const notificationUrl =
  process.env.NOTIFICATION_WORKER_URL || '/api/tasks/signing-notification-worker';
await fetch(notificationUrl, {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    Authorization: `Bearer ${await getWorkerToken()}`,
  },
  body: JSON.stringify({
    type: 'invitation',
    organizationId: body.organizationId,
    signingRequestId: model.id,
  }),
});
```

Or simpler: Import and call `processNotification()` directly (if running in the same environment). The cleanest approach depends on whether services and functions share a runtime.

**Step 2: Integrate reminders**

Remove the `// TODO: Queue reminder emails for pending signers (Task 22)` comment in `remindSigners()` and add:

```typescript
const pendingEmails = pendingSigners.map(s => s.email);
// Call notification worker with type: 'reminder' and signerEmails filter
```

**Step 3: Run tests**

```bash
pnpm --filter @pactosigna/services test
```

**Step 4: Commit**

```bash
git add apps/services/src/modules/signing-requests/SigningRequestsService.ts
git commit -m "feat: integrate email notifications for invitations and reminders"
```

---

### Task 11: Add "Download Signed PDF" to detail page

**Files:**

- Modify: `apps/web/src/features/signing-requests/pages/SigningRequestDetailPage.tsx`
- Modify: `apps/web/src/shared/services/api/signing-requests-api.ts` (add download function if needed)

**Step 1: Add download button**

When the signing request status is 'completed' and `signedDocumentPaths` exists, show a "Download Signed PDF" button. This should trigger a download of the signed PDF from Cloud Storage.

Options:

- Generate a signed download URL on the backend: `GET /signing-requests/:id/download` → returns a redirect to a time-limited Cloud Storage URL
- Or use Firebase Storage client-side download

Backend approach is more secure. Add a new route that generates a signed URL and redirects.

**Step 2: Run typecheck**

```bash
pnpm --filter @pactosigna/web typecheck
```

**Step 3: Commit**

```bash
git add apps/web/src/features/signing-requests/ apps/services/src/modules/signing-requests/
git commit -m "feat: add Download Signed PDF button to detail page"
```

---

### Phase F: Cleanup and Testing (Tasks 12-13)

### Task 12: Run full integration checks

**Step 1: Full typecheck**

```bash
pnpm typecheck
```

**Step 2: Run all unit tests**

```bash
pnpm test
```

**Step 3: Lint and format**

```bash
pnpm lint:fix && pnpm format
```

Fix any issues.

**Step 4: Commit**

```bash
git add -A
git commit -m "chore: fix lint and formatting across signing requests"
```

---

### Task 13: Run code simplifier and final verification

**Step 1: Run code simplifier**

Use the `code-simplifier` agent on all new/modified files:

- `apps/services/src/modules/signing-requests/`
- `apps/web/src/features/signing-requests/`
- `apps/web/src/shared/services/api/signing-ceremony-api.ts`
- `packages/data/src/models/signingRequestModels.ts`
- `packages/data/src/repositories/SigningRequestsRepository.ts`

**Step 2: Run tests after simplification**

```bash
pnpm test
```

**Step 3: Final commit**

```bash
git add -A
git commit -m "chore: simplify signing requests code"
```

---

## Summary Table

| Phase                    | Tasks       | What Gets Built                                                                   |
| ------------------------ | ----------- | --------------------------------------------------------------------------------- |
| **A: Dependencies**      | Task 1      | Install `@react-pdf-viewer/core`, `react-signature-canvas`, `pdfjs-dist`          |
| **B: Backend Ceremony**  | Tasks 2-4   | Signing ceremony service methods, unauthenticated API routes, PDF upload endpoint |
| **C: Frontend Creation** | Tasks 5-7   | PDF uploader, Field Placement Editor, Signature Capture, upgraded New page        |
| **D: Frontend Ceremony** | Tasks 8-9   | Public API client, signing ceremony hook, full External Signing Page              |
| **E: Email Integration** | Tasks 10-11 | Invitation/reminder email triggers, download signed PDF button                    |
| **F: Cleanup**           | Tasks 12-13 | Typecheck, lint, format, code simplifier, final verification                      |

## Dependency Graph

```
Task 1 (deps) ──┐
                 ├── Task 5 (PDF components) ── Task 7 (New page upgrade)
                 │        │
                 │        └── Task 6 (signature capture) ── Task 9 (external signing page)
                 │
Task 2 (ceremony service) ── Task 3 (ceremony routes) ── Task 8 (public API client) ── Task 9
                 │
Task 4 (upload endpoint) ── Task 7 (New page upgrade)
                 │
Task 10 (email integration) ── can run after Task 2
Task 11 (download button) ── can run after Task 3
                 │
Tasks 12-13 (cleanup) ── runs last
```

## Parallelization Opportunities

These task groups are independent and can run in parallel:

- **Group 1**: Tasks 2-3 (backend ceremony) + Task 4 (upload endpoint)
- **Group 2**: Tasks 5-6 (frontend components) — depends on Task 1
- **Group 3**: Task 10 (email integration) — depends on Task 2
- **Group 4**: Task 11 (download button) — depends on Task 3

After Groups 1+2 complete: Task 7 (New page) and Tasks 8-9 (External signing) can proceed.
