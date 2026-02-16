---
id: RISK-US-006
title: 'Audit Trail Navigation'
status: draft
links:
  - type: derives-from
    target: UN-004
  - type: mitigated-by
    target: SDD-006
---

# Audit Trail Navigation

## 1. Purpose

This document assesses the usability risk that auditors cannot efficiently navigate audit logs to find specific events, creating delays during regulatory inspections. This is a use-related risk per IEC 62366-1 impacting audit readiness.

## 2. Risk Category

| Field       | Value                                            |
| ----------- | ------------------------------------------------ |
| Category    | Usability                                        |
| Subcategory | Information retrieval — audit log navigation     |
| Standard    | IEC 62366-1, ISO 14971, 21 CFR Part 11 §11.10(e) |

## 3. Hazard Identification

### 3.1 Hazard Description

Audit logs accumulate large volumes of chronological entries across all bounded contexts. Without effective filtering, search, and pagination, auditors may be unable to locate specific events (e.g., a particular signature action or document change) within the time constraints of a regulatory inspection, leading to audit delays or findings of inadequate record accessibility.

### 3.2 Foreseeable Sequence of Events

1. External auditor requests evidence of a specific action (e.g., who approved release v2.1)
2. QA Manager navigates to the audit log and begins searching
3. Log contains thousands of entries with no effective filtering or search
4. User scrolls through pages of irrelevant entries, unable to locate the target event
5. Auditor records a finding for inadequate audit trail accessibility per 21 CFR Part 11

### 3.3 Affected Users

- Auditors (external) conducting regulatory inspections
- QA Managers responding to audit queries
- Regulatory Specialists preparing pre-audit evidence packages

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating               | Justification                                                              |
| -------------- | -------------------- | -------------------------------------------------------------------------- |
| Severity       | 3 — Moderate         | Audit delays are costly but correctable; not a direct patient safety issue |
| Probability    | 3 — Occasional       | Large log volumes with basic navigation are a known auditor pain point     |
| Detectability  | 2 — Good             | Slow retrieval is self-evident during audit sessions                       |
| **Risk Level** | **Medium (RPN: 18)** | 3 × 3 × 2                                                                  |

### After Mitigation

| Factor            | Rating           | Justification                                                              |
| ----------------- | ---------------- | -------------------------------------------------------------------------- |
| Severity          | 3 — Moderate     | Inherent severity of audit delay unchanged                                 |
| Probability       | 1 — Remote       | ViewModel hooks with filtering and pagination enable rapid event retrieval |
| Detectability     | 1 — High         | Search results surface matching events immediately                         |
| **Residual Risk** | **Low (RPN: 3)** | 3 × 1 × 1                                                                  |

## 5. Risk Mitigation

| #   | Mitigation Measure                                                   | Type   | Target ID | Status      |
| --- | -------------------------------------------------------------------- | ------ | --------- | ----------- |
| 1   | ViewModel hooks with server-side filtering by event type, user, date | Design | SDD-006    | Implemented |
| 2   | Paginated audit log with configurable page size                      | Design | SDD-006    | Implemented |
| 3   | Export audit log subset to CSV/PDF for offline auditor review        | Design | SDD-006    | Planned     |

## 6. Verification of Mitigation

| Mitigation # | Verification Method            | Evidence                          | Result  |
| ------------ | ------------------------------ | --------------------------------- | ------- |
| 1            | E2E test — filter combinations | Audit log filter accuracy tests   | Pending |
| 2            | Usability evaluation           | Event retrieval time < 30 seconds | Pending |
| 3            | E2E test — export flow         | CSV/PDF export content tests      | Pending |

## 7. Residual Risk Acceptability

Residual risk is **acceptable**. Server-side filtering via ViewModel hooks enables auditors to locate specific events within seconds. Pagination prevents UI performance degradation with large datasets, and planned export functionality will support offline audit workflows.

## 8. Traceability

| Direction | Link Type    | Target ID | Title                    |
| --------- | ------------ | --------- | ------------------------ |
| Up        | derives-from | UN-004    | Audit Trail & Compliance |
| Down      | mitigated-by | SDD-006    | ViewModel Hooks Pattern  |

## 9. Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-16 | —      | Initial draft |
