---
id: SRS-405
title: 'Audit Actions Enum'
status: draft
links:
  - type: derives-from
    target: PRS-010
---

# Audit Actions Enum

## Requirement

> **SRS-405.1:** The system **SHALL** define all audit actions in a shared TypeScript enum/type using the format `{resource}.{verb}` (e.g., `release.created`).
>
> **SRS-405.2:** The system **SHALL** enforce valid action strings at compile time via TypeScript type checking.
>
> **SRS-405.3:** The system **SHALL** maintain documentation for when each action is logged, covering all defined actions across all bounded contexts.

## Context

A consistent audit action vocabulary ensures uniform logging across the codebase. The enum is defined in the shared package and used by all Cloud Functions. Actions follow the `{resource}.{verb}` naming convention for readability and searchability. The full list includes actions for organization, member, repository, document, release, and training operations.

## Classification

| Field           | Value |
| --------------- | ----- |
| Safety Class    | B     |
| Category        | Data  |
| Bounded Context | Audit |

## Detailed Specification

### Inputs

- N/A (compile-time definition)

### Processing

- Define TypeScript enum/type in shared package.
- All Cloud Functions import and use the enum for audit logging.
- TypeScript compiler enforces valid action strings.

### Outputs

- Shared type definition for audit actions
- Compile-time enforcement of valid action strings

### Error Handling

- Invalid action string: TypeScript compilation error (caught at build time)

## Acceptance Criteria

1. **Given** the audit actions definition, **when** a developer writes a new audit log call, **then** TypeScript enforces that only valid action strings are used.
2. **Given** the enum, **when** listing all values, **then** each action follows the `{resource}.{verb}` format.
3. **Given** the documentation, **when** reviewing any action, **then** the trigger condition for that action is documented.
4. **Given** a developer using an undefined action string, **when** compiling, **then** a TypeScript error is raised.

## Traceability

| Direction | Link Type    | Target ID | Title                         |
| --------- | ------------ | --------- | ----------------------------- |
| Up        | derives-from | PRS-010    | Audit log viewing & filtering |

## Source

**STORY-405 — Audit Actions Enum**

> **As a** developer **I want** a defined list of audit actions **So that** logging is consistent across the codebase.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
