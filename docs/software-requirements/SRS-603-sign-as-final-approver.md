---
id: SRS-603
title: 'Sign as Final Approver'
status: draft
links:
  - type: derives-from
    target: PRS-014
---

# Sign as Final Approver

## Requirement

> **SRS-603.1:** The system **SHALL** allow a member of the `finalApproverDepartment` (per OrganizationSettings) to provide a final approval signature only after all required department signatures have been collected.
>
> **SRS-603.2:** The system **SHALL** trigger a release status change to "approved" upon accepting the final approver signature.
>
> **SRS-603.3:** The system **SHALL** require re-authentication before accepting the final approver signature.
>
> **SRS-603.4:** The system **SHALL** create an audit log entry for the final approver signature.

## Context

The final approver signature is the quality gate that transitions a release from "in_review" to "approved". Only members of the designated `finalApproverDepartment` can provide this signature. The signature is created with `signatureType: 'final_approver'`.

## Classification

| Field           | Value              |
| --------------- | ------------------ |
| Safety Class    | B                  |
| Category        | Security           |
| Bounded Context | Release Management |

## Detailed Specification

### Inputs

- Release ID
- Signature meaning
- Re-authentication credentials

### Processing

1. Validate all required department signatures are collected.
2. Validate signer is from the `finalApproverDepartment`.
3. Re-authenticate the signer.
4. Create signature record with `signatureType: 'final_approver'`.
5. Transition release status to "approved".
6. Create audit log entry.

### Outputs

- Final approver signature record
- Release status changed to "approved"
- Audit log entry

### Error Handling

- Missing department signatures: reject with "All required department signatures must be collected first"
- Signer not from final approver department: reject with "You must be from the final approver department"
- Re-authentication failed: reject signing

## Acceptance Criteria

1. **Given** a release with all department signatures collected, **when** a final approver signs, **then** the release status changes to "approved".
2. **Given** a release missing department signatures, **when** final approval is attempted, **then** it is rejected.
3. **Given** a signer not from the final approver department, **when** they attempt to provide final approval, **then** it is rejected.
4. **Given** a successful final approval, **when** examining the audit log, **then** the `release.signature_added` action is recorded.

## Traceability

| Direction | Link Type    | Target ID | Title             |
| --------- | ------------ | --------- | ----------------- |
| Up        | derives-from | PRS-014    | Signature capture |

## Source

**STORY-603 — Sign as Final Approver**

> **As a** Final Approver (Quality Manager) **I want** to provide final approval signature **So that** the release can be approved.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
