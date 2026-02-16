# ADR-013: File Naming Conventions

## Status

Accepted

## Date

2026-01-27

## Context

As the codebase grows with new packages (`@pactosigna/contracts`, `@pactosigna/data`, `@pactosigna/services`), we need consistent file naming conventions. Inconsistent naming creates confusion:

- Should `RisksService` class live in `risks.service.ts` or `RisksService.ts`?
- Should utility functions live in `matrix.ts` or `calculateRiskMatrix.ts`?
- What about controllers that export functions vs classes?

### Options Considered

| Option                 | Example                                   | Pros                                                | Cons                                               |
| ---------------------- | ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------- |
| **A) Dot notation**    | `risks.service.ts`, `risks.controller.ts` | Role visible in filename, Angular/NestJS convention | Doesn't match export name, requires mental mapping |
| **B) Export-matching** | `RisksService.ts`, `risksController.ts`   | Predictable - filename matches export               | Role not in filename (folder structure helps)      |
| **C) kebab-case**      | `risks-service.ts`, `risks-controller.ts` | Consistent casing, URL-safe                         | Doesn't match JS export conventions                |

### Considerations

1. **Predictability**: Developers should know the filename from the export name
2. **Discoverability**: Easy to find files in IDE/file tree
3. **Consistency**: Same convention across all packages
4. **TypeScript conventions**: Follow community norms

## Decision

We will use **export-matching naming** with these rules:

### 1. Single-Export Files: Match the Export Name

The filename matches the primary export exactly (including case):

```
RisksService.ts      → export class RisksService
risksController.ts   → export function risksController
RisksRepository.ts   → export class RisksRepository
DomainError.ts       → export class DomainError
```

**Why case matters**:

- Classes use PascalCase → `RisksService.ts`
- Functions use camelCase → `risksController.ts`
- Constants use UPPER_CASE → file uses descriptive name

### 2. Multi-Export Utility Files: Qualified Topic Name

When a file exports multiple related utilities, use a descriptive topic name with domain qualifier:

```
riskMatrix.ts        → exports calculateRiskMatrix, getAcceptabilityStatus
riskGaps.ts          → exports detectRiskGaps, groupGapsByType
```

**Not** generic names like `matrix.ts` or `utils.ts` - always include domain context.

### 3. Index Files: Always `index.ts`

Barrel exports use `index.ts`:

```
risks/
  index.ts           → re-exports from other files
  risksController.ts
  RisksService.ts
```

### 4. Test Files: Match Source with `.test.ts` Suffix

```
RisksService.ts      → RisksService.test.ts
risksController.ts   → risksController.test.ts
riskMatrix.ts        → riskMatrix.test.ts
```

### 5. Configuration Files: kebab-case

Build/config files follow ecosystem conventions:

```
eslint.config.js
vitest.config.ts
tsconfig.json
esbuild.config.js
```

## Examples

### Controller Module (apps/services)

```
src/modules/risks/
  index.ts              → export { risksController, RisksService }
  risksController.ts    → export function risksController(app)
  RisksService.ts       → export class RisksService
  riskMatrix.ts         → export function calculateRiskMatrix, getAcceptabilityStatus
  riskGaps.ts           → export function detectRiskGaps, groupGapsByType
```

### Repository Package (packages/data)

```
src/repositories/
  index.ts              → export { RisksRepository, MembersRepository }
  RisksRepository.ts    → export class RisksRepository
  MembersRepository.ts  → export class MembersRepository
```

### Contracts Package (packages/contracts)

```
src/risks/
  index.ts              → re-exports
  requests.ts           → exports multiple request schemas (topic-based)
  responses.ts          → exports multiple response schemas (topic-based)
```

## Consequences

### Positive

- **Predictable**: Know the filename from the import
- **IDE-friendly**: Auto-import suggestions match expectations
- **Grep-friendly**: Search for `RisksService` finds both file and usages
- **Consistent**: Same pattern across all packages

### Negative

- **Mixed case in folder listings**: `RisksService.ts` and `risksController.ts` adjacent
- **Requires judgment**: Multi-export files need descriptive topic names

### Migration

Existing code should be migrated opportunistically when touching those files. New code must follow these conventions.

## References

- [Google TypeScript Style Guide](https://google.github.io/styleguide/tsguide.html#file-encoding-and-naming)
- [Angular Style Guide](https://angular.io/guide/styleguide#naming) (for comparison with dot notation)
