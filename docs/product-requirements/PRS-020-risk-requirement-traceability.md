---
id: PRS-020
title: 'Risk-Requirement Traceability'
status: draft
links:
  - type: derives-from
    target: UN-008
---

# Risk-Requirement Traceability

## Requirement Statements

1. The system **SHALL** support frontmatter `mitigates` links from risk documents to the documents they address: usability risks link to User Needs, software risks link to Requirements, and security risks link to Architecture documents.

2. The system **SHALL** enable identification of which risk assessments may need review when a linked document (User Need, Requirement, or Architecture) is modified in a sync event.

3. The system **SHALL** provide a view showing, for any given document, all associated risk documents with their category, status, and mitigation completeness.

4. The system **SHALL** identify risk coverage gaps — documents that should have associated risks but do not (e.g., Requirements without software risk analysis).

5. The system **SHALL** make risk traceability data queryable via MCP servers, supporting AI-assisted queries such as "What are the unmitigated risks for this device?" or "Which requirements lack risk analysis?"

## Rationale

ISO 14971 §3.4 requires that residual risk is evaluated and documented, with traceability between hazards, hazardous situations, risks, and risk control measures. IEC 62304 §7.1 requires software risk analysis that traces to software requirements. Without explicit traceability between risks and the items they address, an organization cannot demonstrate to regulators that risk management is comprehensive and current. Automated gap detection prevents unanalyzed requirements from reaching production.

## Classification

| Field    | Value                            |
| -------- | -------------------------------- |
| Priority | Essential                        |
| Category | Functional / Safety / Regulatory |
| Standard | ISO 14971 §3.4, IEC 62304 §7.1   |

## Acceptance Criteria

1. Given a software risk document with `links: [{type: mitigates, target: REQ-001}]`, when synced, then a Link document is created connecting the risk to REQ-001 with link type "mitigates"
2. Given REQ-001 is modified in a new commit, when the sync completes, then the system can identify that the software risk mitigating REQ-001 may need review (stale risk indicator)
3. Given a user viewing REQ-001's detail page, when the linked risks section loads, then all risk documents that mitigate REQ-001 are shown with their category and status
4. Given 10 Requirements in a device repository where 3 have no associated software risk documents, when the risk coverage gap view is loaded, then those 3 Requirements are identified as lacking risk analysis
5. Given an MCP query "What are the unmitigated risks for CardioMonitor?", then the qms-risks server returns all risk documents for CardioMonitor with open/unmitigated status

## Traceability

| Direction | Link Type    | Target ID | Title                           |
| --------- | ------------ | --------- | ------------------------------- |
| Up        | derives-from | UN-008    | Risk Management                 |
| Related   | related-to   | PRS-008    | Document Linking & Traceability |
| Related   | related-to   | PRS-021    | Traceability Matrix Generation  |
| Down      | refined-by   | —         | _(To be linked by SWR-xxx)_     |
| Down      | verified-by  | —         | _(To be linked by TP-xxx)_      |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
