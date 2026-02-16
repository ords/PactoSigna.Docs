# ADR-014: Shared Package Purpose and Scope

## Status

Accepted

## Context

The monorepo has three packages for type sharing:

- `@pactosigna/shared` - Originally contained domain types, schemas, constants, and utilities
- `@pactosigna/contracts` - Contains API request/response DTOs
- `@pactosigna/data` - Contains backend service implementations

This created confusion about what belongs where, especially regarding:

- Should entity types (Device, Release, etc.) live in shared or contracts?
- Should validation schemas be duplicated or shared?
- What's the boundary between "shared" and "contract"?

**The Problem:** If frontend and backend both import entity types from `@pactosigna/shared`, it creates tight coupling. Any database schema change would force frontend changes, violating the principle that API contracts should isolate implementation details.

**The Insight:** The frontend doesn't need to know about backend database schemas. The backend doesn't need to know about frontend ViewModels. They only need to agree on the **API contract** (DTOs) and **domain logic** (validation rules, calculations, predicates).

## Decision

`@pactosigna/shared` will contain **only domain logic that must execute identically on both client and server**, specifically:

### 1. Validation Rules (Zod Schemas for Enums and Constraints)

```typescript
// Frontend: Validate form input before submit
// Backend: Validate API request before persist
export const SafetyClassSchema = z.enum(['A', 'B', 'C']);
export const DeviceNameSchema = z.string().min(1).max(100);
export const EmailSchema = z.string().email();
```

**Why both sides?**

- Frontend: Immediate UX feedback (show validation errors instantly)
- Backend: Enforcement (ensure data integrity)

### 2. Business Rule Predicates (Pure Functions)

```typescript
// Frontend: Show "Sign" button only if eligible
// Backend: Enforce user can actually sign
export function canUserSignDocument(
  user: { department: string },
  doc: { requiredDepartments: string[] }
): boolean {
  return doc.requiredDepartments.includes(user.department);
}
```

**Why both sides?**

- Frontend: Enable/disable UI controls
- Backend: Enforce authorization rules

### 3. Domain Calculations (Pure Functions)

```typescript
// Frontend: Display risk score in UI
// Backend: Store calculated score in database
export function calculateRiskScore(severity: number, probability: number): number {
  return severity * probability;
}
```

**Why both sides?**

- Frontend: Show computed values immediately
- Backend: Persist consistent calculated values

### 4. Constants (Domain Vocabulary)

```typescript
// Frontend: Populate dropdown options
// Backend: Validate incoming values
export const DOCUMENT_TYPES = ['SRS', 'SDS', 'RISK', 'TEST'] as const;
export const SAFETY_CLASSES = ['A', 'B', 'C'] as const;
```

**Why both sides?**

- Frontend: Build UI controls (dropdowns, radio buttons)
- Backend: Validate enum values

### 5. Type Guards (Domain Predicates)

```typescript
// Frontend: Type narrowing in components
// Backend: Type narrowing in controllers
export function isDevice(owner: Owner): owner is Device {
  return owner.ownerType === 'device';
}
```

**Why both sides?**

- Frontend: TypeScript type narrowing for conditional rendering
- Backend: TypeScript type narrowing for business logic

### 6. Document Parsing Rules (Markdown Frontmatter Schemas)

```typescript
// Frontend: Preview risk document structure
// Backend: Parse and validate risk documents from GitHub
export const RiskEntryFrontmatterSchema = z.object({
  severity: z.number().int().min(1).max(5),
  probability: z.number().int().min(1).max(5),
  // ...
});
```

**Why both sides?**

- Frontend: Validate document structure in editor preview
- Backend: Parse and validate documents from repository sync

### What Does NOT Go in Shared

❌ **Complete entity structures** - Use `@pactosigna/contracts` DTOs instead

```typescript
// ❌ WRONG - Don't put this in shared
export interface Device {
  id: string;
  name: string;
  safetyClass: SafetyClass;
  // ... 10+ fields
}

// ✅ RIGHT - Put this in @pactosigna/contracts
export interface GetDeviceResponse {
  /* ... */
}
```

❌ **HTTP request/response shapes** - Belongs in `@pactosigna/contracts`
❌ **Database schemas** - Backend only (e.g., Firestore document structure)
❌ **Component state interfaces** - Frontend only
❌ **API client code** - Frontend only
❌ **Database queries** - Backend only

## Architecture Flow

```
Frontend ViewModel ← API DTO (contract) → Backend Database Schema
     (apps/web)      (@pactosigna/contracts)     (backend)
          ↓                      ↓                    ↓
     Uses shared validators, calculations, predicates
                (@pactosigna/shared)
```

**Example:**

```typescript
// Frontend (apps/web)
import { GetDeviceResponse } from '@pactosigna/contracts';
import { SafetyClassSchema, canUserModifyDevice } from '@pactosigna/shared';

function DeviceForm() {
  const [safetyClass, setSafetyClass] = useState('A');

  // Validate using shared schema
  const validation = SafetyClassSchema.safeParse(safetyClass);

  // Check permission using shared predicate
  const canModify = canUserModifyDevice(currentUser, device);

  // Use DTO as ViewModel
  const device: GetDeviceResponse = await api.getDevice(id);
}

// Backend (packages/data)
import { GetDeviceRequest, GetDeviceResponse } from '@pactosigna/contracts';
import { SafetyClassSchema, canUserModifyDevice } from '@pactosigna/shared';

export async function getDevice(req: GetDeviceRequest): Promise<GetDeviceResponse> {
  // Validate using shared schema
  SafetyClassSchema.parse(req.safetyClass);

  // Check permission using shared predicate
  if (!canUserModifyDevice(req.user, dbDoc)) {
    throw new ForbiddenError();
  }

  // Map database schema to DTO
  return {
    id: dbDoc.id,
    name: dbDoc.name,
    safetyClass: dbDoc.safetyClass,
    // ...
  };
}
```

## Consequences

### Positive

✅ **Clear separation of concerns:** Domain logic vs. data shape
✅ **Reduced coupling:** Frontend doesn't depend on backend database schema
✅ **Consistent validation:** Same rules enforced on both sides
✅ **Better DX:** Clear decision tree for where code goes
✅ **Smaller shared package:** ~50-100 lines instead of ~240 lines
✅ **Testable business rules:** Pure functions are easy to test

### Negative

⚠️ **Migration needed:** Existing code imports entity types from shared
⚠️ **Learning curve:** Team must understand the distinction
⚠️ **Potential duplication:** Some validation logic might feel duplicated (but it's intentional)

### Migration Strategy

1. Keep enum validators in `@pactosigna/shared`
2. Delete full entity schemas from `@pactosigna/shared`
3. Frontend imports DTOs from `@pactosigna/contracts` as ViewModels
4. Backend maps database schemas to DTOs when responding to API calls

## Examples

### ✅ Good - Belongs in Shared

```typescript
// packages/shared/src/schemas/index.ts

// Enum validators (business rules about valid values)
export const SafetyClassSchema = z.enum(['A', 'B', 'C']);
export const DocumentTypeSchema = z.enum(['SRS', 'SDS', 'RISK']);

// Field constraints (business rules about valid inputs)
export const DeviceNameValidator = z.string().min(1).max(100);
export const EmailValidator = z.string().email();

// Business predicates (rules that apply everywhere)
export function canUserSignDocument(
  user: { department: string },
  doc: { requiredDepartments: string[] }
): boolean {
  return doc.requiredDepartments.includes(user.department);
}

// Domain calculations (must be consistent everywhere)
export function calculateRiskScore(severity: number, probability: number): number {
  return severity * probability;
}
```

### ❌ Bad - Belongs in Contracts

```typescript
// ❌ Don't put this in shared - it's a DTO
export const DeviceSchema = z.object({
  id: z.string(),
  organizationId: z.string(),
  repositoryId: z.string(),
  name: z.string().min(1).max(100),
  safetyClass: z.enum(['A', 'B', 'C']),
  createdAt: z.date(),
  createdBy: z.string(),
});

// ✅ Put this in @pactosigna/contracts instead
export interface GetDeviceResponse {
  id: string;
  organizationId: string;
  repositoryId: string;
  name: string;
  safetyClass: 'A' | 'B' | 'C';
  createdAt: string; // ISO timestamp
  createdBy: string;
}
```

## Enforcement

### PR Review Checklist

When reviewing PRs that add or modify `@pactosigna/shared`:

1. **Does the new export describe data shape or domain logic?** Data shape → contracts.
2. **Does the type name end in `Response`, `Request`, or `DTO`?** It belongs in contracts.
3. **Is it a Zod `z.object()` with fields like `id`, `title`, `status`?** Likely an entity/DTO — belongs in contracts.
4. **Is it a `z.enum()`, `z.string().min()`, or pure function?** Likely domain logic — belongs in shared.

### Quick Decision Tree

```
Is it a z.enum or scalar constraint?  → shared
Is it a pure function/predicate?      → shared
Is it a frontmatter parsing schema?   → shared
Is it a constant/config?              → shared
Is it an API response/request shape?  → contracts
Is it a full entity with id + fields? → contracts (or data layer)
```

### Approved Shared Content (Risk Domain)

These items are confirmed as legitimate shared content:

- `RiskGapCodeSchema` / `RiskGapSeveritySchema` — domain enums
- `AcceptabilityStatusSchema` — domain enum (single source of truth; contracts should re-export, not redeclare)
- `RiskEntryFrontmatterSchema`, `HazardFrontmatterSchema`, `HazardousSituationFrontmatterSchema`, `HarmFrontmatterSchema` — markdown parsing rules
- `RiskMatrixConfigSchema` — repository configuration validation
- `MitigationSchema` / `MitigationTargetSchema` — domain value objects
- `DEFAULT_ACCEPTABILITY_CONFIG`, `DEFAULT_RISK_MATRIX_LABELS` — domain constants

## Violations Found

### 2026-02-05: Risk Response DTOs in Shared

**What happened:** `RiskMatrixResponseSchema`, `RiskGapsResponseSchema`, `RiskSummarySchema`, and `RiskGapSchema` were added to `@pactosigna/shared` on 2026-01-23, three days before `@pactosigna/contracts` got its own risk response schemas on 2026-01-26. The shared versions were never deleted.

**Impact:** Frontend components (`RiskHeatmap`, `RiskGapsAccordion`) imported from shared, while the API client (`risks-api.ts`) correctly imported from contracts. This created a translation layer in `RiskMatrixPage.tsx` with `as unknown as` casts, fabricated summary fields (`withMitigations: 0`, `gapsCount: 0`), and field renames (`inherent` → `inherentMatrix`).

**Additional issues found:**

- `AcceptabilityStatusSchema` is declared independently in both shared and contracts instead of contracts importing from shared
- `riskMatrix.ts` (backend) internal type includes dead fields (`withMitigations`, `gapsCount`) modeled after the shared DTO
- `groupGapsByType()` in `riskGaps.ts` returns `Record<string, number>` (matching shared's shape) but is unused — `RisksService` does its own grouping returning `Record<string, RiskGap[]>` (matching contracts' shape)
- `packages/shared/src/types/` still contains full entity interfaces (`RiskListItem`, `RiskDetail`, `Release`, `ProductRelease`, etc.) that should live in contracts or the data layer

**Root cause:** ADR-014 was written but had no enforcement mechanism. The risk feature was implemented before contracts schemas existed, and the migration step was never tracked or completed.

**Fix:** See Phase 2 scope in the migration strategy below.

## References

- ADR-008: Backend-Only Firestore Writes
- ADR-011: REST API Migration
- Martin Fowler - DTO Pattern: https://martinfowler.com/eaaCatalog/dataTransferObject.html
- Domain-Driven Design - Shared Kernel: https://www.domainlanguage.com/ddd/

## Summary

> **@pactosigna/shared contains domain logic (validations, calculations, predicates, constants) that must execute identically on both client and server. Client-side for immediate UX feedback. Server-side for enforcement and data integrity.**

**Key Principle:** If it's a business rule that affects user experience AND must be enforced by the backend → shared. If it's just data shape → contracts.
