---
id: PRS-021
title: 'Traceability Matrix Generation & Visualization'
status: draft
links:
  - type: derives-from
    target: UN-009
---

# Traceability Matrix Generation & Visualization

## Requirement Statements

1. The system **SHALL** provide an interactive traceability matrix view where users select a source document type and target document type (e.g., Requirements → Tests) and see a matrix of relationships.

2. The system **SHALL** calculate and display coverage percentages for each traceability pair (e.g., "75% of Requirements have linked Tests").

3. The system **SHALL** visually highlight gaps — source documents that lack expected linked targets — using color coding or icons in the matrix.

4. The system **SHALL** provide a gap detection report covering all standard traceability patterns: User Needs without Requirements, Requirements without Tests, Requirements without upstream User Needs, and Architecture without implementing Requirements.

5. The system **SHALL** allow users to click through from any cell in the traceability matrix to the linked document's detail view.

6. The system **SHALL** support filtering the traceability matrix by document status (e.g., show only effective documents).

7. The system **SHALL** provide full upstream and downstream trace chain navigation from any document, showing the complete traceability path from User Needs through Tests and associated Risks.

8. The system **SHALL** support exporting the traceability matrix and gap reports as CSV for inclusion in regulatory submissions.

## Rationale

IEC 62304 §5.1.1 requires traceability between system requirements, software requirements, software architecture, software detailed design, software units, and their verification. FDA design control guidance expects bidirectional traceability demonstrating that every user need is addressed by requirements, every requirement is verified by tests, and every architectural decision is linked to requirements. The traceability matrix is a primary audit artifact — auditors use it to independently verify design control completeness. Automated gap detection eliminates the manual, error-prone process of checking traceability spreadsheets before audits.

## Classification

| Field    | Value                                                   |
| -------- | ------------------------------------------------------- |
| Priority | Essential                                               |
| Category | Functional / Regulatory                                 |
| Standard | IEC 62304 §5.1.1, FDA Design Controls, ISO 13485 §7.3.3 |

## Acceptance Criteria

1. Given a user selecting "Requirements" as source and "Tests" as target, when the matrix loads, then each Requirement is shown as a row with its linked Test documents (or gap indicator)
2. Given 20 Requirements where 15 have linked Tests, then the coverage percentage displays "75%"
3. Given a Requirement without a linked Test, then its row in the matrix is highlighted with a gap indicator (e.g., red cell or warning icon)
4. Given the gap detection report, when generated, then it lists documents missing expected links across all standard patterns with counts per pattern
5. Given a cell in the matrix showing REQ-001 → TC-001, when clicked, then the user navigates to the TC-001 detail view
6. Given the matrix filtered by status "effective", when applied, then only effective documents are included in the matrix and coverage calculations
7. Given a user viewing REQ-001, when they request the full trace chain, then the system shows: upstream (UN-xxx → REQ-001) and downstream (REQ-001 → TEST-xxx, REQ-001 → risk documents)
8. Given a gap report, when exported as CSV, then the file contains document ID, document title, missing link type, and expected target type for each gap

## Traceability

| Direction | Link Type    | Target ID | Title                           |
| --------- | ------------ | --------- | ------------------------------- |
| Up        | derives-from | UN-009    | Traceability Matrix             |
| Related   | related-to   | PRS-008    | Document Linking & Traceability |
| Related   | related-to   | PRS-020    | Risk-Requirement Traceability   |
| Down      | refined-by   | —         | _(To be linked by SWR-xxx)_     |
| Down      | verified-by  | —         | _(To be linked by TP-xxx)_      |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
