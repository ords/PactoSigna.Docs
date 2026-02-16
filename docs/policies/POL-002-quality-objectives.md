---
id: POL-002
title: 'Quality Objectives'
status: draft
links:
  - type: derives-from
    target: POL-001
  - type: implemented-by
    target: SOP-010
---

# Quality Objectives

## 1. Purpose

This policy defines measurable quality objectives for PactoSigna's QMS platform. These objectives provide quantifiable targets for monitoring quality performance and driving continual improvement, as required by ISO 13485 §5.4.1.

## 2. Scope

These objectives apply to the PactoSigna product and all personnel involved in its design, development, testing, and support. Objectives are reviewed during management reviews (SOP-010) and updated annually or when significant changes occur.

## 3. Policy Statement

> PactoSigna shall maintain measurable quality objectives that are consistent with the Quality Policy (POL-001), relevant to product conformity and regulatory compliance, and reviewed at planned intervals to ensure continued suitability.

## 4. Definitions

| Term              | Definition                                                          |
| ----------------- | ------------------------------------------------------------------- |
| Quality Objective | A measurable target related to quality performance                  |
| KPI               | Key Performance Indicator — the metric used to measure an objective |
| Target            | The threshold value that defines success for a quality objective    |
| Review Period     | The interval at which objectives are measured and evaluated         |

## 5. Requirements

### 5.1 Traceability Completeness

- **Objective:** 100% of product requirements shall have at least one linked verification artifact
- **KPI:** Requirements-to-Tests coverage percentage (PRS-021 traceability matrix)
- **Target:** 100% at each release
- **Review:** Per release cycle + quarterly management review

### 5.2 Audit Trail Availability

- **Objective:** Audit log entries shall be available within 24 hours of any QMS mutation
- **KPI:** Time between mutation and audit log entry availability
- **Target:** < 24 hours (real-time in practice; 24h is the compliance threshold)
- **Review:** Quarterly management review

### 5.3 Training Compliance Rate

- **Objective:** 95% or higher training completion rate across all active training tasks
- **KPI:** (Completed training tasks / Total assigned training tasks) × 100
- **Target:** ≥ 95% completion within due date
- **Review:** Monthly dashboard review + quarterly management review

### 5.4 Signature Integrity

- **Objective:** 100% of published releases shall have all required signatures
- **KPI:** Releases published with complete signatures / Total published releases
- **Target:** 100% (enforced by system design)
- **Review:** Per release + quarterly management review

### 5.5 CAPA Closure Rate

- **Objective:** 90% of corrective/preventive actions shall be closed within their target date
- **KPI:** CAPAs closed on time / Total CAPAs with target dates
- **Target:** ≥ 90%
- **Review:** Quarterly management review

## 6. Roles and Responsibilities

| Role            | Responsibility                                                 |
| --------------- | -------------------------------------------------------------- |
| Leadership      | Approve objectives, allocate resources for improvement         |
| QA Manager      | Measure and report on objectives, recommend target adjustments |
| Product Manager | Ensure product features support objective measurement          |
| All Personnel   | Contribute to achieving quality objectives in their daily work |

## 7. Compliance

Quality objectives are reviewed during management reviews (SOP-010). When an objective is not met:

1. Root cause analysis is performed
2. Corrective action is initiated per SOP-009
3. Objective targets are evaluated for continued appropriateness
4. Results are documented in the management review record

## 8. Related Documents

| ID      | Title                            | Relationship           |
| ------- | -------------------------------- | ---------------------- |
| POL-001 | Quality Policy                   | Parent policy          |
| SOP-009 | Corrective and Preventive Action | CAPA process           |
| SOP-010 | Management Review                | Implements this policy |

## 9. Regulatory References

- ISO 13485:2016 §5.4.1 — Quality objectives
- ISO 13485:2016 §5.6 — Management review
- ISO 13485:2016 §8.4 — Analysis of data

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
