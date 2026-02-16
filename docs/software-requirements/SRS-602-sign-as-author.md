---
id: SRS-602
title: 'Sign as Author'
status: draft
links:
  - type: derives-from
    target: PRS-014
---

# Sign as Author

## Requirement

> **SRS-602.1:** The system **SHALL** allow a document author to sign a release with `signatureType: 'author'` to confirm authorship of the changes.
>
> **SRS-602.2:** The system **SHALL** require re-authentication before accepting the author signature.
>
> **SRS-602.3:** The system **SHALL** default the signature meaning to "I am the author of these changes" while allowing custom meaning text.
>
> **SRS-602.4:** The system **SHALL** create an audit log entry for the author signature.

## Context

Author signatures provide authorship attestation for document changes in a release. The default meaning streamlines the signing process while allowing customization. Author determination may be enhanced in the future via commit metadata matching.

## Classification

| Field           | Value              |
| --------------- | ------------------ |
| Safety Class    | B                  |
| Category        | Security           |
| Bounded Context | Release Management |

## Detailed Specification

### Inputs

- Release ID
- Signature meaning (defaults to "I am the author of these changes", customizable)
- Re-authentication credentials

### Processing

1. Validate release is in "in_review" status.
2. Re-authenticate the signer.
3. Create signature record with `signatureType: 'author'`.
4. Store signature in release and subcollection.
5. Create audit log entry.

### Outputs

- Author signature record
- Audit log entry

### Error Handling

- Re-authentication failed: reject signing
- Release not in review: reject with appropriate message

## Acceptance Criteria

1. **Given** a document author, **when** they sign a release, **then** a signature of type 'author' is created.
2. **Given** an author signing without custom meaning, **when** the signature is created, **then** the meaning defaults to "I am the author of these changes".
3. **Given** an author providing custom meaning, **when** the signature is created, **then** the custom meaning is stored.
4. **Given** re-authentication failure, **when** signing is attempted, **then** the signature is rejected.

## Traceability

| Direction | Link Type    | Target ID | Title             |
| --------- | ------------ | --------- | ----------------- |
| Up        | derives-from | PRS-014    | Signature capture |

## Source

**STORY-602 — Sign as Author**

> **As a** Document Author **I want** to sign a release as the author **So that** I confirm authorship of the changes.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
