---
id: PRS-014
title: 'Electronic Signature Capture'
status: draft
links:
  - type: derives-from
    target: UN-006
---

# Electronic Signature Capture

## Requirement Statements

1. The system **SHALL** require re-authentication (re-entering password) before accepting any electronic signature to confirm the signer's identity.

2. The system **SHALL** capture the following data with each signature: signer identity (user ID, name, email, department), signature type (Author, Reviewer, Approver, Trainee, Auditor), stated meaning, server-generated timestamp, and the commit SHAs of signed content.

3. The system **SHALL** enforce signature immutability — once created, a signature record cannot be edited, modified, or deleted by any user or system process.

4. The system **SHALL** enforce that a user can only sign a given resource (release or training task) once, preventing duplicate signatures.

5. The system **SHALL** enforce ordering constraints: a Final Approver can only sign after all required departmental reviewer signatures have been collected.

6. The system **SHALL** log failed authentication attempts during the signing ceremony in the audit trail, including the user identity and timestamp.

7. The system **SHALL** lock out the signing ceremony after 3 consecutive failed authentication attempts, requiring the user to wait or contact an administrator.

8. The system **SHALL** bind each signature to the specific content version being signed (commit SHAs), ensuring that a signature cannot be transferred to a different version.

## Rationale

FDA 21 CFR Part 11 §11.50 requires that electronic signatures be linked to their respective electronic records so that signatures cannot be excised, copied, or otherwise transferred to falsify records. §11.100 requires that each electronic signature be unique to one individual and not reused by anyone else. §11.200 requires verification of the identity of the signer at the time of signing. These requirements collectively ensure the legal validity and regulatory defensibility of electronic signatures in the system.

## Classification

| Field    | Value                                       |
| -------- | ------------------------------------------- |
| Priority | Essential                                   |
| Category | Regulatory / Safety                         |
| Standard | FDA 21 CFR Part 11 §11.50, §11.100, §11.200 |

## Acceptance Criteria

1. Given a user initiating a signature, when they enter an incorrect password, then the signature is not accepted and a failed attempt is logged in the audit trail
2. Given a user who enters the correct password and selects a meaning, when the signature is submitted, then a signature record is created with all required fields (identity, type, meaning, timestamp, commit SHAs)
3. Given a completed signature record, when any attempt is made to update or delete it (via API or direct Firestore access), then the operation is denied
4. Given a user who has already signed a release as Department Reviewer, when they attempt to sign the same release again as Department Reviewer, then the system rejects the duplicate
5. Given a release with 3 required department signatures and only 2 collected, when the Final Approver attempts to sign, then the system prevents the signature and indicates which departments have not yet signed
6. Given 3 consecutive failed authentication attempts during signing, then the user is locked out of the signing ceremony with a message to contact an administrator
7. Given a signature record, when queried, then the commit SHAs match exactly the document versions that were included in the release at the time of signing

## Traceability

| Direction | Link Type    | Target ID | Title                       |
| --------- | ------------ | --------- | --------------------------- |
| Up        | derives-from | UN-006    | Electronic Signatures       |
| Down      | refined-by   | —         | _(To be linked by SWR-xxx)_ |
| Down      | verified-by  | —         | _(To be linked by TP-xxx)_  |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
