---
id: RISK-SW-001
title: 'Document Sync Data Loss'
status: draft
links:
  - type: derives-from
    target: PRS-006
  - type: mitigated-by
    target: SOP-002
---

# Document Sync Data Loss

## 1. Purpose

This document assesses the risk of the document sync engine corrupting, losing, or incorrectly processing documents during synchronization from GitHub to Firestore. This is a critical data integrity risk for a QMS SaMD per ISO 14971 and IEC 62304 Class C requirements.

## 2. Risk Category

| Field       | Value                                     |
| ----------- | ----------------------------------------- |
| Category    | Software                                  |
| Subcategory | Data integrity — document synchronization |
| Standard    | ISO 14971, IEC 62304, ISO 13485 §4.2.4    |

## 3. Hazard Identification

### 3.1 Hazard Description

The document sync engine may corrupt document content, lose documents, drop frontmatter metadata, or create inconsistent state between GitHub (source of truth) and Firestore (operational store) during synchronization. This includes partial sync failures where some documents update while others do not, resulting in an internally inconsistent QMS state.

### 3.2 Foreseeable Sequence of Events

1. User or webhook triggers a document sync from a GitHub repository
2. Sync worker fetches files from GitHub and begins processing
3. A failure occurs mid-sync (network timeout, malformed frontmatter, Cloud Tasks crash, Firestore write failure)
4. Some documents are updated in Firestore while others retain stale versions
5. Traceability links reference incorrect document versions or are missing entirely
6. Organization relies on incomplete/incorrect QMS data for regulatory decisions

### 3.3 Affected Users / Stakeholders

- QA Managers relying on accurate document state for release decisions
- Regulatory Specialists preparing audit documentation
- External Auditors reviewing document history and traceability
- All organization members viewing potentially stale documents

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating             | Justification                                                                                          |
| -------------- | ------------------ | ------------------------------------------------------------------------------------------------------ |
| Severity       | 4 — Major          | Loss of document integrity undermines entire QMS; could lead to regulatory submissions with wrong data |
| Probability    | 3 — Occasional     | Network failures, malformed files, and partial writes are realistic during routine sync operations     |
| Detectability  | 3 — Moderate       | Sync failures may not be immediately visible; users must check sync event status                       |
| **Risk Level** | **High (RPN: 36)** | Severity × Probability × Detectability = 4 × 3 × 3                                                     |

### After Mitigation

| Factor            | Rating           | Justification                                                                                         |
| ----------------- | ---------------- | ----------------------------------------------------------------------------------------------------- |
| Severity          | 4 — Major        | Severity of data loss remains inherently high regardless of controls                                  |
| Probability       | 1 — Remote       | Idempotent sync, changelog tracking, and graceful error handling reduce likelihood of undetected loss |
| Detectability     | 1 — High         | Sync event status tracking, parse error flags, and changelog diffs make failures immediately visible  |
| **Residual Risk** | **Low (RPN: 4)** | 4 × 1 × 1                                                                                             |

## 5. Risk Mitigation

| #   | Mitigation Measure                                                        | Type    | Target ID | Status      |
| --- | ------------------------------------------------------------------------- | ------- | --------- | ----------- |
| 1   | Sync event status tracking (pending → in_progress → completed/failed)     | Design  | PRS-006    | Implemented |
| 2   | Immutable changelog entries for every synced document with commit SHA     | Design  | PRS-006    | Implemented |
| 3   | Graceful handling of malformed frontmatter (log warning, flag error)      | Design  | PRS-006    | Implemented |
| 4   | Background processing via Cloud Tasks to prevent timeout-related failures | Design  | PRS-006    | Implemented |
| 5   | Deleted files marked as removed but history preserved                     | Design  | PRS-006    | Implemented |
| 6   | Document control SOP requiring sync verification before release           | Process | SOP-002   | Implemented |

## 6. Verification of Mitigation

| Mitigation # | Verification Method                | Evidence                       | Result  |
| ------------ | ---------------------------------- | ------------------------------ | ------- |
| 1            | Unit + integration tests           | Sync event state machine tests | Pending |
| 2            | Unit tests                         | Changelog creation tests       | Pending |
| 3            | Unit tests with malformed YAML     | Parser error handling tests    | Pending |
| 4            | E2E test with Cloud Tasks emulator | Sync completion under load     | Pending |
| 5            | Unit test for file deletion sync   | Soft-delete preservation tests | Pending |
| 6            | Process audit                      | SOP-002 compliance records     | Pending |

## 7. Residual Risk Acceptability

Residual risk is **acceptable**. The combination of sync event tracking, immutable changelogs, and graceful error handling ensures that any sync anomaly is immediately detectable. The document control SOP (SOP-002) requires verification of sync completion before any release activity. The system's design ensures GitHub remains the source of truth and re-sync can always recover from a failed state.

## 8. Traceability

| Direction | Link Type    | Target ID | Title                |
| --------- | ------------ | --------- | -------------------- |
| Up        | derives-from | PRS-006    | Document Sync Engine |
| Down      | mitigated-by | SOP-002   | Document Control     |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
