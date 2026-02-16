---
id: UN-005
title: 'Release Management'
status: draft
links:
  - type: derives-from
    target: US-UP-001
  - type: derives-from
    target: US-UP-003
  - type: derives-from
    target: epic-05-release-management
---

# Release Management

## Need Statement

> As a **QA Manager**, I need **to bundle document changes into versioned releases, submit them for departmental review, collect all required signatures, and publish immutable baselines** so that **every change to the QMS follows a formal, auditable change control process as required by ISO 13485 and FDA 21 CFR Part 11**.

## Context

Release management is the formal change control mechanism in PactoSigna. After engineers merge document changes via GitHub pull requests (Stage 1: Technical Review), the QA Manager creates a Quality Controlled Change Release in PactoSigna (Stage 2: Formal Approval). This two-stage review ensures both technical accuracy and regulatory compliance.

A release bundles changes from one or more repositories (QMS-only, device-only, or combined) and progresses through a defined lifecycle: Draft → In Review → Approved → Published. The system automatically calculates which departments must sign based on the types of documents included and the organization's reviewer rules. Once published, a release becomes an immutable baseline — visible to auditors and serving as the official record of what changed and who approved it.

The Regulatory Specialist (Dr. Maria Santos persona) participates as a reviewer and is concerned that no unauthorized modifications occur after submission. The QA Manager needs clear visibility into approval progress across releases.

## Stakeholder

| Field   | Value                                                             |
| ------- | ----------------------------------------------------------------- |
| Persona | QA Manager (Sarah Chen), Regulatory Specialist (Dr. Maria Santos) |
| Source  | Epic 05 — Release Management; Product Vision §Release Management  |

## Acceptance Criteria

1. A Quality Manager can create a release bundle by selecting documents from any connected repository, with a name, version, and description
2. Selected documents are locked to their current commit SHA at the time of inclusion
3. Draft releases can be edited (add/remove documents); non-draft releases are immutable
4. Submitting a release for review automatically calculates required reviewer departments based on included document types and organization reviewer rules
5. Release status progresses through Draft → In Review → Approved → Published with clear state transitions
6. All required reviewers are notified when a release is submitted for review
7. A release can only be approved after all required department signatures are collected
8. Published releases are immutable and represent auditable baselines
9. Release list supports filtering by status (draft, in_review, approved, published) and type (qms, device, combined)
10. Every release lifecycle event (create, edit, submit, approve, publish) is audit-logged

## Traceability

| Direction | Link Type      | Target ID                  | Title                         |
| --------- | -------------- | -------------------------- | ----------------------------- |
| Up        | derives-from   | US-UP-001                  | QA Manager Persona            |
| Up        | derives-from   | US-UP-003                  | Regulatory Specialist Persona |
| Down      | implemented-by | epic-05-release-management | Epic 5: Release Management    |
| Down      | implemented-by | STORY-501                  | Create Release Bundle         |
| Down      | implemented-by | STORY-502                  | Edit Draft Release            |
| Down      | implemented-by | STORY-503                  | Submit Release for Review     |
| Down      | implemented-by | STORY-504                  | View Release Details          |
| Down      | implemented-by | STORY-505                  | List Releases                 |
| Down      | implemented-by | STORY-506                  | Approve Release               |
| Down      | implemented-by | STORY-507                  | Publish Release               |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
