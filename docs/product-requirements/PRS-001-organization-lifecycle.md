---
id: PRS-001
title: 'Organization Lifecycle Management'
status: draft
links:
  - type: derives-from
    target: UN-001
---

# Organization Lifecycle Management

## Requirement Statements

1. The system **SHALL** allow a verified, authenticated user to create a new organization by providing a unique organization name and selecting applicable regulatory frameworks (ISO 13485, IEC 62304, FDA 21 CFR 820).

2. The system **SHALL** automatically assign the creating user the Owner role upon organization creation.

3. The system **SHALL** enforce organization name uniqueness across all organizations in the system.

4. The system **SHALL** allow organization administrators to configure organization-level settings, including department list, default regulatory frameworks, and reviewer assignment rules per document type.

5. The system **SHALL** prevent deletion of an organization while it contains active members, connected repositories, or unpublished releases.

6. The system **SHALL** record every organization mutation (creation, settings change, configuration update) as an immutable audit log entry with actor identity, server-generated timestamp, and before/after state.

## Rationale

ISO 13485 requires a defined organizational structure with clear quality management responsibilities. FDA 21 CFR Part 820 mandates documented organizational procedures. The organization entity is the root aggregate in PactoSigna — no other domain activity (document management, releases, signatures, training) can occur without an established organization. Proper lifecycle management ensures the organizational foundation is audit-ready from inception.

## Classification

| Field    | Value                             |
| -------- | --------------------------------- |
| Priority | Essential                         |
| Category | Functional                        |
| Standard | ISO 13485 §5.5, FDA 21 CFR 820.20 |

## Acceptance Criteria

1. Given a verified user, when they submit a valid organization name and at least one regulatory framework, then an organization is created and the user is assigned the Owner role
2. Given an organization name that already exists, when a user attempts to create an organization with that name, then the system rejects the request with a descriptive error
3. Given an organization administrator, when they update organization settings (departments, reviewer rules), then the changes are persisted and an audit log entry is created
4. Given an organization with active members, when an admin attempts to delete the organization, then the system prevents the deletion and returns an error
5. Given any organization mutation, then an immutable audit log entry is created containing the actor's identity, timestamp, action, and before/after state

## Traceability

| Direction | Link Type    | Target ID | Title                       |
| --------- | ------------ | --------- | --------------------------- |
| Up        | derives-from | UN-001    | Organization & Team Setup   |
| Down      | refined-by   | —         | _(To be linked by SWR-xxx)_ |
| Down      | verified-by  | —         | _(To be linked by TP-xxx)_  |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
