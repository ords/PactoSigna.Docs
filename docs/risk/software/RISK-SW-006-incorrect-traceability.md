---
id: RISK-SW-006
title: 'Incorrect Traceability'
status: draft
links:
  - type: derives-from
    target: PRS-021
  - type: mitigated-by
    target: SOP-003
---

# Incorrect Traceability

## 1. Purpose

This document assesses the risk of the traceability matrix displaying incorrect, incomplete, or misleading link relationships between documents. False traceability data could lead to a false sense of compliance completeness and undermine regulatory submissions.

## 2. Risk Category

| Field       | Value                                            |
| ----------- | ------------------------------------------------ |
| Category    | Software                                         |
| Subcategory | Data integrity — traceability and design control |
| Standard    | ISO 14971, IEC 62304 §5.1.1, ISO 13485 §7.3.3    |

## 3. Hazard Identification

### 3.1 Hazard Description

The traceability matrix may display broken, stale, or phantom links due to: (a) frontmatter link extraction errors during sync, (b) stale links persisting after documents are deleted or renamed, (c) incorrect coverage percentage calculations, or (d) gap detection failing to identify missing required links. This gives users a false compliance picture.

### 3.2 Foreseeable Sequence of Events

1. Documents are synced with frontmatter containing `links` arrays
2. Link extraction or storage introduces errors (wrong target IDs, missing links, phantom links to deleted documents)
3. Traceability matrix displays incorrect coverage percentages (e.g., shows 100% when actual coverage is lower)
4. Gap detection fails to flag documents missing required downstream links
5. Organization submits regulatory documentation with incomplete traceability
6. Auditor discovers traceability gaps that the system failed to detect

### 3.3 Affected Users / Stakeholders

- QA Managers relying on traceability matrix for release decisions
- Regulatory Specialists using traceability for regulatory submissions
- External Auditors verifying design control completeness
- All organization members trusting traceability data

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating             | Justification                                                                               |
| -------------- | ------------------ | ------------------------------------------------------------------------------------------- |
| Severity       | 4 — Major          | False traceability picture can lead to regulatory submissions with undiscovered gaps        |
| Probability    | 3 — Occasional     | Link extraction from frontmatter, stale references, and coverage math are error-prone areas |
| Detectability  | 4 — Low            | False positives (claiming coverage exists when it doesn't) are particularly hard to detect  |
| **Risk Level** | **High (RPN: 48)** | 4 × 3 × 4                                                                                   |

### After Mitigation

| Factor            | Rating               | Justification                                                                                     |
| ----------------- | -------------------- | ------------------------------------------------------------------------------------------------- |
| Severity          | 4 — Major            | Severity of false traceability remains high for compliance                                        |
| Probability       | 2 — Unlikely         | Automated gap detection, link validation during sync, and queryable link storage reduce errors    |
| Detectability     | 2 — Good             | Gap detection report with coverage percentages and explicit gap highlighting makes issues visible |
| **Residual Risk** | **Medium (RPN: 16)** | 4 × 2 × 2                                                                                         |

## 5. Risk Mitigation

| #   | Mitigation Measure                                                 | Type    | Target ID | Status      |
| --- | ------------------------------------------------------------------ | ------- | --------- | ----------- |
| 1   | Frontmatter links extracted and stored as queryable Link documents | Design  | PRS-006    | Implemented |
| 2   | Coverage percentage calculation for each traceability pair         | Design  | PRS-021    | Planned     |
| 3   | Visual gap highlighting (color coding for missing links)           | Design  | PRS-021    | Planned     |
| 4   | Gap detection report covering all standard traceability patterns   | Design  | PRS-021    | Planned     |
| 5   | CSV export for independent verification of traceability data       | Design  | PRS-021    | Planned     |
| 6   | Design control SOP requiring traceability review before release    | Process | SOP-003   | Implemented |

## 6. Verification of Mitigation

| Mitigation # | Verification Method              | Evidence                           | Result  |
| ------------ | -------------------------------- | ---------------------------------- | ------- |
| 1            | Unit test — link extraction      | Frontmatter link parsing tests     | Pending |
| 2            | Unit test — coverage calculation | Coverage percentage accuracy tests | Pending |
| 3            | E2E test — gap visualization     | Gap highlighting in matrix view    | Pending |
| 4            | Integration test — gap detection | Gap report completeness tests      | Pending |
| 5            | E2E test — CSV export            | Export data accuracy verification  | Pending |
| 6            | Process audit                    | SOP-003 compliance records         | Pending |

## 7. Residual Risk Acceptability

Residual risk is **acceptable** with monitoring. The residual RPN of 16 (medium) reflects the inherent complexity of traceability data. The automated gap detection report is the primary safety net — it proactively identifies missing links before they become audit findings. The CSV export capability enables independent verification of traceability data. The design control SOP (SOP-003) mandates traceability review as a prerequisite for release publication, providing a process-level check.

## 8. Traceability

| Direction | Link Type    | Target ID | Title                                          |
| --------- | ------------ | --------- | ---------------------------------------------- |
| Up        | derives-from | PRS-021    | Traceability Matrix Generation & Visualization |
| Down      | mitigated-by | SOP-003   | Design Control                                 |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
