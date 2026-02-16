---
id: SRS-105
title: 'Organization Settings'
status: draft
links:
  - type: derives-from
    target: PRS-002
---

# Organization Settings

## Requirement

> **SRS-105.1:** The system **SHALL** provide an organization settings page accessible only to users with the admin role.
>
> **SRS-105.2:** The system **SHALL** allow admins to update organization name, regulatory frameworks, reviewer rules (mapping document types to required reviewer departments and final approver department), and the department list.
>
> **SRS-105.3:** The system **SHALL** validate that at least one admin exists in the final approver department before accepting reviewer rule changes.
>
> **SRS-105.4:** The system **SHALL** create an audit log entry for every settings change via the `updateOrganizationSettings` Cloud Function.

## Context

Organization settings define how document review, approval workflows, and department structures operate. Default reviewer rules map document types (Architecture, Requirement, SOP, Work Instruction, Risk) to required reviewer departments and final approver departments. The `OrganizationSettingsUpdated` domain event is fired on changes.

## Classification

| Field           | Value             |
| --------------- | ----------------- |
| Safety Class    | A                 |
| Category        | Functional        |
| Bounded Context | Identity & Access |

## Detailed Specification

### Inputs

- Organization name (updated value)
- Regulatory frameworks (updated multi-select)
- Reviewer rules: document type → required departments + final approver department
- Department list additions/removals

### Processing

1. Validate admin authorization.
2. Validate at least one admin in final approver department.
3. Update organization document via Cloud Function.
4. Fire `OrganizationSettingsUpdated` domain event.
5. Create audit log entry.

### Outputs

- Updated organization settings in Firestore
- Audit log entry for settings change

### Error Handling

- Non-admin access: return 403 Forbidden
- No admin in final approver department: reject with descriptive error
- Invalid reviewer rule mapping: display validation error

## Acceptance Criteria

1. **Given** an admin user, **when** they navigate to organization settings, **then** the settings page is displayed with current values.
2. **Given** a non-admin user, **when** they attempt to access settings, **then** access is denied.
3. **Given** an admin updating reviewer rules, **when** the final approver department has no admin members, **then** the change is rejected with an error message.
4. **Given** an admin updating any setting, **when** the change is saved, **then** an audit log entry is created.
5. **Given** an admin adding a custom department, **when** they save, **then** the new department appears in the department list.

## Traceability

| Direction | Link Type    | Target ID | Title                  |
| --------- | ------------ | --------- | ---------------------- |
| Up        | derives-from | PRS-002    | Member role management |

## Source

**STORY-105 — Organization Settings**

> **As an** organization admin **I want** to configure organization settings **So that** I can customize PactoSigna for my company's processes.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
