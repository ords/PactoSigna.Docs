---
id: SRS-601
title: 'Sign as Department Reviewer'
status: draft
links:
  - type: derives-from
    target: PRS-014
---

# Sign as Department Reviewer

## Requirement

> **SRS-601.1:** The system **SHALL** allow a member of a required department to sign a release that is in "in_review" status, and limit each member to one signature per release.
>
> **SRS-601.2:** The system **SHALL** require the signer to enter a signature meaning (e.g., "Reviewed and approved") and re-authenticate by re-entering their password before the signature is accepted.
>
> **SRS-601.3:** The system **SHALL** record the signature with user ID, email, department, server-generated timestamp, meaning, and the commit SHAs of all included documents.
>
> **SRS-601.4:** The system **SHALL** create an audit log entry for each department reviewer signature.

## Context

Department reviewer signatures are the foundation of the release approval workflow. Each signature is immutable and bound to specific commit SHAs, ensuring the signer approved exact document versions. Re-authentication satisfies Part 11 requirements for signature verification. Signatures are stored both in the release document and in a separate signatures subcollection for querying.

## Classification

| Field           | Value              |
| --------------- | ------------------ |
| Safety Class    | B                  |
| Category        | Security           |
| Bounded Context | Release Management |

## Detailed Specification

### Inputs

- Release ID
- Signature meaning (free text)
- Re-authentication credentials (password)

### Processing

1. Validate release is in "in_review" status.
2. Validate signer is a member of a required department.
3. Validate signer has not already signed this release.
4. Re-authenticate the signer via Firebase Auth.
5. Create signature record with `signatureType: 'dept_reviewer'`.
6. Bind signature to all commit SHAs in the release.
7. Store in release signatures array and signatures subcollection.
8. Create audit log entry.

### Outputs

- Signature record (immutable)
- Updated release signature progress
- Audit log entry

### Error Handling

- Not a member of a required department: reject with "You are not in a required reviewer department"
- Already signed: reject with "You have already signed this release"
- Re-authentication failed: reject with "Invalid password"
- Release not in review: reject with "Release is not currently in review"

## Acceptance Criteria

1. **Given** a member of a required department, **when** they sign a release in review, **then** a signature is recorded with their identity and meaning.
2. **Given** a signer, **when** re-authentication fails, **then** the signature is rejected.
3. **Given** a member who already signed, **when** they attempt to sign again, **then** the duplicate is rejected.
4. **Given** a successful signature, **when** examining the record, **then** the commit SHAs of all documents are included.

## Traceability

| Direction | Link Type    | Target ID | Title             |
| --------- | ------------ | --------- | ----------------- |
| Up        | derives-from | PRS-014    | Signature capture |

## Source

**STORY-601 — Sign as Department Reviewer**

> **As a** Department Reviewer **I want** to sign a release on behalf of my department **So that** my department's review is recorded.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
