---
id: PRS-019
title: 'Risk Matrix Definition & Visualization'
status: draft
links:
  - type: derives-from
    target: UN-008
---

# Risk Matrix Definition & Visualization

## Requirement Statements

1. The system **SHALL** support three distinct risk categories: usability risk (IEC 62366 analysis), software risk (FMEA with severity, probability, detectability), and security risk (STRIDE threat modeling).

2. The system **SHALL** infer risk document type from the file's directory path during sync: `/risks/usability/` for usability risks, `/risks/software/` for software risks, and `/risks/security/` for security risks.

3. The system **SHALL** provide a risk register view per device, filterable by risk category, linked document, and mitigation status (open, mitigated, accepted).

4. The system **SHALL** display a risk matrix visualization for software risks showing severity vs. probability, with color-coded risk levels (e.g., green/yellow/orange/red).

5. The system **SHALL** clearly identify unmitigated risks — risks without linked mitigation controls or with open status — in the risk register and matrix views.

6. The system **SHALL** display risk data per device, accessible through the device switcher, ensuring risk analysis is always viewed in the correct product context.

## Rationale

ISO 14971 requires systematic identification, estimation, evaluation, control, and monitoring of risks throughout the product lifecycle. IEC 62366 requires usability risk analysis. The risk matrix is a core audit artifact that demonstrates the organization's risk management process is active and current. Per-device risk visualization ensures that each medical device's risk profile is independently assessable, as required by regulatory submissions.

## Classification

| Field    | Value                              |
| -------- | ---------------------------------- |
| Priority | Essential                          |
| Category | Functional / Safety                |
| Standard | ISO 14971, IEC 62366, IEC 62304 §7 |

## Acceptance Criteria

1. Given risk documents in `/risks/usability/`, `/risks/software/`, and `/risks/security/`, when synced, then each is classified with the correct risk category
2. Given a risk register for "CardioMonitor" device, when filtered by "software risk", then only FMEA-based software risk documents for that device are shown
3. Given software risks with severity and probability ratings in frontmatter, when the risk matrix view is loaded, then risks are plotted on a severity × probability grid with appropriate color coding
4. Given a risk document with no `mitigates` links to mitigation controls, when viewed in the risk register, then it is visually flagged as unmitigated
5. Given the device switcher set to "InsulinPump", when viewing the risk register, then only risks from the InsulinPump repository are displayed
6. Given a risk register, when filtered by mitigation status "open", then only risks with open/unmitigated status are shown

## Traceability

| Direction | Link Type    | Target ID | Title                       |
| --------- | ------------ | --------- | --------------------------- |
| Up        | derives-from | UN-008    | Risk Management             |
| Down      | refined-by   | —         | _(To be linked by SWR-xxx)_ |
| Down      | verified-by  | —         | _(To be linked by TP-xxx)_  |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
