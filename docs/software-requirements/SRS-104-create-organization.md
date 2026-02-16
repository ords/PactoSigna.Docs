---
id: SRS-104
title: 'Create Organization'
status: draft
links:
  - type: derives-from
    target: PRS-002
---

# Create Organization

## Requirement

> **SRS-104.1:** The system **SHALL** provide an organization creation wizard that collects organization name (required, 3–100 characters) and regulatory frameworks (multi-select: ISO 13485, IEC 62304, FDA 21 CFR 820).
>
> **SRS-104.2:** The system **SHALL** automatically add the creating user as an admin member of the new organization and require them to select a department from a predefined list or enter a custom department.
>
> **SRS-104.3:** The system **SHALL** create the organization via a Cloud Function that generates an audit log entry for the creation event.
>
> **SRS-104.4:** The system **SHALL** redirect the user to the "Connect GitHub" step after successful organization creation.
>
> **SRS-104.5:** The system **SHALL** allow duplicate organization names but display a warning when a duplicate is detected.

## Context

Organization creation is the first step after user signup. The Cloud Function `createOrganization` creates the organization document and member document in Firestore. Default departments (Engineering, Quality, Regulatory, Security, Clinical, Operations, Executive) are pre-populated. Domain events `OrganizationCreated` and `MemberJoined` are fired.

## Classification

| Field           | Value             |
| --------------- | ----------------- |
| Safety Class    | A                 |
| Category        | Functional        |
| Bounded Context | Identity & Access |

## Detailed Specification

### Inputs

- Organization name (3–100 characters, required)
- Regulatory frameworks (multi-select, at least one recommended)
- Department selection (from predefined list or custom entry)

### Processing

1. Validate organization name length and format.
2. Create `/organizations/{orgId}` document in Firestore via Cloud Function.
3. Create `/organizations/{orgId}/members/{userId}` document with `role: 'admin'`.
4. Fire `OrganizationCreated` and `MemberJoined` domain events.
5. Log audit entry for organization creation.

### Outputs

- New organization document in Firestore
- Admin member document for the creating user
- Audit log entry
- Redirect to "Connect GitHub" onboarding step

### Error Handling

- Organization name too short or too long: display validation error
- Duplicate name detected: display warning (non-blocking)
- Cloud Function failure: display generic error and allow retry

## Acceptance Criteria

1. **Given** a verified user without an organization, **when** they submit the organization creation wizard with a valid name, **then** an organization is created and they are added as admin.
2. **Given** a user creating an organization, **when** the organization name is fewer than 3 or more than 100 characters, **then** validation prevents submission.
3. **Given** a newly created organization, **when** creation succeeds, **then** an audit log entry with action `organization.created` is recorded.
4. **Given** a user entering an organization name that already exists, **when** they type the name, **then** a warning is shown but creation is still allowed.
5. **Given** successful organization creation, **when** the process completes, **then** the user is redirected to the "Connect GitHub" step.

## Traceability

| Direction | Link Type    | Target ID | Title                  |
| --------- | ------------ | --------- | ---------------------- |
| Up        | derives-from | PRS-002    | Member role management |

## Source

**STORY-104 — Create Organization**

> **As a** verified user without an organization **I want** to create a new organization **So that** I can start using PactoSigna for my company.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
