---
id: UN-009
title: 'Traceability Matrix'
status: draft
links:
  - type: derives-from
    target: US-UP-003
  - type: derives-from
    target: US-UP-004
  - type: derives-from
    target: epic-03-document-management
---

# Traceability Matrix

## Need Statement

> As a **Regulatory Specialist**, I need **to view an interactive traceability matrix that shows relationships between document types (User Needs → Requirements → Architecture → Tests) with coverage percentages and gap highlighting** so that **I can verify design control completeness and present audit-ready traceability evidence to FDA reviewers**.

> As an **External Auditor**, I need **to trace from any document through its full upstream and downstream chain — from user needs through requirements, architecture, detailed design, tests, and linked risks** so that **I can independently verify that the design history file is complete and that no requirement exists without verification evidence**.

## Context

IEC 62304 and FDA design control guidance require bidirectional traceability between user needs, software requirements, architecture, implementation, and verification. The traceability model in PactoSigna is:

```
User Needs → Requirements → Architecture → Detailed Design → Tests
     ↓              ↓              ↓
Usability Risk  Software Risk  Security Risk
```

Traceability links are stored in frontmatter and synced to Firestore as queryable Link documents. The traceability matrix view lets users select a source document type and target document type, then displays a matrix showing which source documents have linked targets and which have gaps. Gap detection is critical: requirements without tests, user needs without requirements, and architecture without linked requirements all represent compliance risks that must be addressed before release.

The External Auditor (James Wright persona) expects to perform independent trace-through verification: starting from a complaint or a requirement, tracing the full chain in both directions. The Regulatory Specialist needs to generate coverage reports showing what percentage of requirements have linked tests, exportable as CSV for inclusion in technical files.

## Stakeholder

| Field   | Value                                                                     |
| ------- | ------------------------------------------------------------------------- |
| Persona | Regulatory Specialist (Dr. Maria Santos), External Auditor (James Wright) |
| Source  | Epic 03 §STORY-308/309; Product Vision §Traceability Model                |

## Acceptance Criteria

1. Traceability matrix view allows selecting source and target document types (e.g., Requirements → Tests)
2. Matrix displays all source documents as rows with their linked target documents
3. Coverage percentage is calculated and displayed (e.g., "75% of requirements have tests")
4. Gaps (source documents without linked targets) are visually highlighted
5. Gap report shows documents missing expected links across all standard traceability patterns:
   - User Needs without Requirements
   - Requirements without Tests
   - Requirements without upstream User Needs
   - Architecture without implementing Requirements
6. Users can click through to any document in the matrix
7. Matrix is filterable by status (e.g., only approved requirements)
8. Matrix and gap reports are exportable as CSV
9. Full upstream/downstream trace chain is navigable from any document
10. Gap count is visible in navigation/dashboard for quick compliance assessment

## Traceability

| Direction | Link Type      | Target ID | Title                         |
| --------- | -------------- | --------- | ----------------------------- |
| Up        | derives-from   | US-UP-003 | Regulatory Specialist Persona |
| Up        | derives-from   | US-UP-004 | Auditor Persona               |
| Down      | implemented-by | STORY-308 | Traceability Matrix View      |
| Down      | implemented-by | STORY-309 | Gap Detection                 |
| Related   | related-to     | UN-003    | Document Management & Sync    |
| Related   | related-to     | UN-008    | Risk Management               |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
