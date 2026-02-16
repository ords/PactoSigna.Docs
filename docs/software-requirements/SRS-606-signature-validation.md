---
id: SRS-606
title: 'Signature Validation'
status: draft
links:
  - type: derives-from
    target: PRS-015
---

# Signature Validation

## Requirement

> **SRS-606.1:** The system **SHALL** verify the signer is an active organization member before accepting any signature.
>
> **SRS-606.2:** The system **SHALL** verify the signer is from a required department (for department reviewer signatures).
>
> **SRS-606.3:** The system **SHALL** verify the release is in the correct status for signing ("in_review") and that the user has not already signed the release.
>
> **SRS-606.4:** The system **SHALL** reject invalid signatures with a clear, specific error message.

## Context

Signature validation implements server-side guard logic ensuring only authorized, valid signatures are recorded. This is critical for Part 11 compliance — every signature must be from a verified, authorized signer in the correct context. Validation runs before the signature record is created.

## Classification

| Field           | Value              |
| --------------- | ------------------ |
| Safety Class    | B                  |
| Category        | Security           |
| Bounded Context | Release Management |

## Detailed Specification

### Inputs

- Signer's user ID and member record
- Release ID and current status
- Required departments (from release)
- Existing signatures on the release

### Processing

1. Check signer is an active organization member.
2. Check signer's department matches a required department (for dept signatures).
3. Check release is in "in_review" status.
4. Check signer has not already signed this release.
5. If any check fails, reject with specific error.

### Outputs

- Validation passed: allow signature creation
- Validation failed: specific rejection error

### Error Handling

- Inactive member: "Your account is not active"
- Wrong department: "You are not in a required reviewer department"
- Wrong release status: "This release is not currently accepting signatures"
- Duplicate signature: "You have already signed this release"

## Acceptance Criteria

1. **Given** an inactive member, **when** they attempt to sign, **then** the signature is rejected with "Your account is not active".
2. **Given** a member from a non-required department, **when** they attempt a department review signature, **then** it is rejected.
3. **Given** a release in "draft" status, **when** a signature is attempted, **then** it is rejected with a status error.
4. **Given** a member who already signed, **when** they attempt to sign again, **then** the duplicate is rejected.

## Traceability

| Direction | Link Type    | Target ID | Title                    |
| --------- | ------------ | --------- | ------------------------ |
| Up        | derives-from | PRS-015    | Signing request workflow |

## Source

**STORY-606 — Signature Validation**

> **As a** System **I want** to validate signatures before accepting **So that** only valid signatures are recorded.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
