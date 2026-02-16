---
id: UN-008
title: 'Risk Management'
status: draft
links:
  - type: derives-from
    target: US-UP-001
  - type: derives-from
    target: US-UP-003
---

# Risk Management

## Need Statement

> As a **QA Manager**, I need **to manage risk assessments across three risk categories (usability, software, and security), link risks to the documents they mitigate, and track mitigation status** so that **our risk management process complies with ISO 14971 and IEC 62366, and risk analysis is always current when functionality changes**.

> As a **Regulatory Specialist**, I need **to verify that risk analysis is updated whenever functionality changes, that all risks have linked mitigations, and that I can present a complete risk profile per device to regulators** so that **design control requirements for risk management are met and no unmitigated risks go undetected**.

## Context

ISO 14971 (risk management for medical devices) and IEC 62366 (usability engineering) require systematic identification, evaluation, and control of risks throughout the product lifecycle. PactoSigna supports three distinct risk types, each with its own analysis method and linking pattern:

- **Usability Risk** (IEC 62366): Analyzes use errors and potential harm; links to User Needs documents
- **Software Risk** (FMEA): Evaluates severity, probability, and detectability of software failures; links to Requirements documents
- **Security Risk** (STRIDE): Threat modeling for security vulnerabilities; links to Architecture/Component documents

Risk documents are authored as markdown in the device repository under `/risks/usability/`, `/risks/software/`, and `/risks/security/` directories. They use frontmatter links (`mitigates` link type) to connect to the documents they address. The traceability model ensures every requirement, user need, and architecture component can show its associated risks.

The Regulatory Specialist (Dr. Maria Santos persona) flags any risk analysis that hasn't been updated when the linked functionality changes as a red flag. The QA Manager uses risk registers filtered by type, linked documents, and mitigation status to prepare for audits.

## Stakeholder

| Field   | Value                                                             |
| ------- | ----------------------------------------------------------------- |
| Persona | QA Manager (Sarah Chen), Regulatory Specialist (Dr. Maria Santos) |
| Source  | Product Vision §Risk Types & Linking; ISO 14971; IEC 62366        |

## Acceptance Criteria

1. The system supports three risk categories: usability risk, software risk, and security risk
2. Risk documents are synced from the device repository with correct type inference from directory path (`/risks/usability/`, `/risks/software/`, `/risks/security/`)
3. Risk documents use frontmatter links to connect to the documents they mitigate (User Needs, Requirements, Architecture)
4. Risk register views support filtering by risk type, linked document, and mitigation status
5. Unmitigated risks (risks without linked mitigation controls or open status) are clearly identified
6. Risk matrix per device shows severity/probability/detectability ratings (for FMEA-based software risks)
7. When a linked document changes, the system can identify which risk assessments may need review
8. Risk data is queryable via MCP for AI-assisted analysis (e.g., "What are the unmitigated risks for this device?")
9. All risk document changes follow the same two-stage review process (GitHub PR + PactoSigna release)

## Traceability

| Direction | Link Type    | Target ID | Title                         |
| --------- | ------------ | --------- | ----------------------------- |
| Up        | derives-from | US-UP-001 | QA Manager Persona            |
| Up        | derives-from | US-UP-003 | Regulatory Specialist Persona |
| Related   | related-to   | UN-003    | Document Management & Sync    |
| Related   | related-to   | UN-009    | Traceability Matrix           |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
