---
id: UN-006
title: 'Electronic Signatures'
status: draft
links:
  - type: derives-from
    target: US-UP-003
  - type: derives-from
    target: US-UP-004
  - type: derives-from
    target: US-UN-002
  - type: derives-from
    target: epic-06-signature-collection
---

# Electronic Signatures

## Need Statement

> As a **Regulatory Specialist**, I need **to provide legally binding electronic signatures with re-authentication, meaning capture, and immutable records bound to specific document versions** so that **every approval, review, and attestation meets FDA 21 CFR Part 11 requirements and would withstand regulatory scrutiny**.

> As an **External Auditor**, I need **to verify that electronic signatures are authentic, timestamped, bound to specific content, and include the signer's identity and stated meaning** so that **I can confirm the organization's electronic signature practices are compliant**.

## Context

Electronic signatures are the cornerstone of Part 11 compliance. Every formal action in PactoSigna — departmental review of a release, final approval, training completion, and external audit acknowledgment — requires a Part 11 compliant electronic signature. The signature must be unique to the individual, executed only by the owner, include a stated meaning (e.g., "I have reviewed and approve this document"), carry a server-generated timestamp, and be bound to the specific document versions (commit SHAs) being signed.

The signing ceremony requires re-authentication: the signer must re-enter their password to confirm identity. Failed authentication attempts are logged for security audit. Signatures are immutable once created — they cannot be edited or deleted.

Signature types span the full workflow: Author (confirming authorship), Department Reviewer (reviewing on behalf of a department), Final Approver (granting release approval), Trainee (confirming training completion), and Auditor (acknowledging audit materials). The external signing flow (US-UN-002) extends this to parties outside the organization who sign via secure links without needing a PactoSigna account.

## Stakeholder

| Field   | Value                                                                                                         |
| ------- | ------------------------------------------------------------------------------------------------------------- |
| Persona | Regulatory Specialist (Dr. Maria Santos), External Auditor (James Wright)                                     |
| Source  | Epic 06 — Signature Collection; US-UN-002 — External Signing User Needs; Product Vision §Signatures & Part 11 |

## Acceptance Criteria

1. Signing requires re-authentication (re-entering password) before the signature is accepted
2. Each signature captures: user identity (name, email, department), signature type, stated meaning, server-generated timestamp, and signed commit SHAs
3. Signatures are immutable — no edits or deletions permitted
4. Department reviewers can only sign releases that require their department's review
5. A user can only sign a release once (no duplicate signatures)
6. Final approver can only sign after all required department signatures are collected
7. Failed authentication attempts are logged in the audit trail
8. Maximum 3 authentication attempts before lockout
9. Signature details are viewable by auditors, showing full compliance metadata
10. Signature history is queryable and filterable by signer, department, type, and date range
11. External parties can sign via secure links without creating a PactoSigna account (per US-UN-002 external signing flow)

## Traceability

| Direction | Link Type      | Target ID                    | Title                         |
| --------- | -------------- | ---------------------------- | ----------------------------- |
| Up        | derives-from   | US-UP-003                    | Regulatory Specialist Persona |
| Up        | derives-from   | US-UP-004                    | Auditor Persona               |
| Up        | derives-from   | US-UN-002                    | External Signing User Needs   |
| Down      | implemented-by | epic-06-signature-collection | Epic 6: Signature Collection  |
| Down      | implemented-by | STORY-601                    | Sign as Department Reviewer   |
| Down      | implemented-by | STORY-602                    | Sign as Author                |
| Down      | implemented-by | STORY-603                    | Sign as Final Approver        |
| Down      | implemented-by | STORY-604                    | View Signature Details        |
| Down      | implemented-by | STORY-605                    | Re-authentication for Signing |
| Down      | implemented-by | STORY-606                    | Signature Validation          |
| Down      | implemented-by | STORY-607                    | View Signature History        |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
