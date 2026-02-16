---
id: SRS-302
title: 'Document Type Inference'
status: draft
links:
  - type: derives-from
    target: PRS-006
---

# Document Type Inference

## Requirement

> **SRS-302.1:** The system **SHALL** use the `docType` field from frontmatter as the document type when it is present.
>
> **SRS-302.2:** The system **SHALL** infer the document type from the file's directory path when `docType` is not specified in frontmatter, using the following mapping: `/user-needs/` → `user_need`, `/requirements/` → `requirement`, `/architecture/` → `architecture`, `/detailed-design/` → `detailed_design`, `/tests/` → `test`, `/sops/` → `sop`, `/work-instructions/` → `work_instruction`, `/policies/` → `policy`, `/risks/usability/` → `usability_risk`, `/risks/software/` → `software_risk`, `/risks/security/` → `security_risk`.
>
> **SRS-302.3:** The system **SHALL** set the document type to `unknown` and log a warning when the type cannot be determined from either frontmatter or directory path.

## Context

Document type inference ensures all documents are categorized correctly even when frontmatter is incomplete. The inference is performed during the sync process. Path patterns may become configurable per organization in a future enhancement.

## Classification

| Field           | Value               |
| --------------- | ------------------- |
| Safety Class    | A                   |
| Category        | Data                |
| Bounded Context | Document Management |

## Detailed Specification

### Inputs

- `docType` field from frontmatter (optional)
- File path within the repository

### Processing

1. Check if `docType` is present in frontmatter — if yes, use it.
2. If not, match the file path against the directory-to-type mapping.
3. If no match found, set type to `unknown` and log a warning.

### Outputs

- Document type assigned to the document record

### Error Handling

- No matching path pattern and no frontmatter docType: set to `unknown`, log warning

## Acceptance Criteria

1. **Given** a document with `docType: requirement` in frontmatter, **when** synced, **then** the type is set to `requirement` regardless of file path.
2. **Given** a document without a `docType` field located in `/requirements/`, **when** synced, **then** the type is inferred as `requirement`.
3. **Given** a document without a `docType` field in an unrecognized directory, **when** synced, **then** the type is set to `unknown` and a warning is logged.
4. **Given** a document in `/risks/software/`, **when** synced without a frontmatter docType, **then** the type is inferred as `software_risk`.

## Traceability

| Direction | Link Type    | Target ID | Title                |
| --------- | ------------ | --------- | -------------------- |
| Up        | derives-from | PRS-006    | Document sync engine |

## Source

**STORY-302 — Document Type Inference**

> **As the** PactoSigna system **I want** to infer document type from file path if not in frontmatter **So that** documents are correctly categorized.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
