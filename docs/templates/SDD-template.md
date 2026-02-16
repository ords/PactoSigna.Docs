---
id: '{SSDD-NNN | DSDD-NNN | SDD-NNN}'
title: '{Detailed design document title}'
status: draft
links:
  - type: derives-from
    target: '{ARCH-NNN or SWR-NNN}'
---

# {Title}

> **Document Type**: ISO 13485 Design Output
> **Applies To**: {Module or package scope, e.g., "`apps/web/` (@pactosigna/web)"}

## 1. Purpose

{What this design document covers. This should describe a specific module, subsystem, or cross-cutting concern at implementation detail level.}

## 2. Scope

{What code packages, modules, or features this design covers. Reference the parent architecture document.}

## 3. Design Overview

{High-level description of the design approach. Reference relevant ADRs.}

## 4. Detailed Design

### 4.1 {Module / Class / Component}

**Responsibility**: {What it does}

**Interface**:

```typescript
// Key interfaces or type signatures
```

**Behavior**: {Description of key behaviors, state transitions, or algorithms.}

### 4.2 {Module / Class / Component}

{Repeat for each significant design element.}

## 5. Data Model

{Firestore collections, document schemas, or in-memory data structures relevant to this design.}

| Collection / Type | Fields       | Purpose       |
| ----------------- | ------------ | ------------- |
| {name}            | {key fields} | {description} |

## 6. Error Handling

{How errors are detected, reported, and recovered from in this module.}

## 7. Constraints and Assumptions

- {Constraint or assumption}
- {Additional constraint}

## 8. Traceability

| Direction | Link Type    | Target ID  | Title                         |
| --------- | ------------ | ---------- | ----------------------------- |
| Up        | derives-from | ARCH-{NNN} | {Architecture document title} |
| Up        | implements   | SRS-{NNN}  | {Software requirement title}  |
| Down      | verified-by  | TC-{NNN}   | {Test case title}             |

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | YYYY-MM-DD | {name} | Initial draft |
