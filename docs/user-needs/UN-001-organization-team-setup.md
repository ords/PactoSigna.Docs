---
id: UN-001
title: 'Organization & Team Setup'
status: draft
links:
  - type: derives-from
    target: US-UP-001
  - type: derives-from
    target: epic-01-identity-access
---

# Organization & Team Setup

## Need Statement

> As a **QA Manager**, I need **to create an organization, configure departments, define reviewer rules, and invite team members with appropriate roles** so that **our company can begin using a centralized QMS with proper access controls and role-based permissions from day one**.

## Context

Medical device companies operating under ISO 13485 and FDA 21 CFR Part 11 require a structured organizational hierarchy where personnel roles, departments, and access rights are clearly defined. When a QA Manager decides to adopt PactoSigna, they must be able to stand up the organizational structure quickly — creating the organization, selecting applicable regulatory frameworks (ISO 13485, IEC 62304, FDA 21 CFR 820), inviting team members across Engineering, Quality, Regulatory, Security, and other departments, and assigning roles (admin, member, auditor). Without this foundation, no other QMS activity (document management, releases, signatures, training) can proceed.

This need arises during initial onboarding and whenever the organization grows — new hires must be invited, departing members removed, and roles updated. The QA Manager (Sarah Chen persona) is accountable for ensuring the right people have the right access and that every organizational change is audit-logged.

## Stakeholder

| Field   | Value                                              |
| ------- | -------------------------------------------------- |
| Persona | QA Manager (Sarah Chen)                            |
| Source  | Epic 01 — Identity & Access; Product Vision §Users |

## Acceptance Criteria

1. A verified user can create a new organization with a name and selected regulatory frameworks
2. The creating user is automatically assigned the admin role
3. Admins can invite team members by email, specifying department and role (admin / member / auditor)
4. Invitees can accept invitations and join the organization (with or without an existing account)
5. Admins can manage members: update department, change role, remove members
6. Admins can configure organization settings including department list and reviewer assignment rules per document type
7. Every organizational mutation (create, invite, join, role change, removal) is recorded in the immutable audit log
8. At least one admin must exist in the organization at all times (cannot remove or demote the last admin)

## Traceability

| Direction | Link Type      | Target ID               | Title                     |
| --------- | -------------- | ----------------------- | ------------------------- |
| Up        | derives-from   | US-UP-001               | QA Manager Persona        |
| Down      | implemented-by | epic-01-identity-access | Epic 1: Identity & Access |
| Down      | implemented-by | STORY-101               | User Signup               |
| Down      | implemented-by | STORY-104               | Create Organization       |
| Down      | implemented-by | STORY-105               | Organization Settings     |
| Down      | implemented-by | STORY-106               | Invite Team Members       |
| Down      | implemented-by | STORY-107               | Accept Invite             |
| Down      | implemented-by | STORY-108               | Manage Members            |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
