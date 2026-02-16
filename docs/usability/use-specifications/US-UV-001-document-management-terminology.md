# Document Management - Terminology

## User-Facing Labels

| Label           | Definition                                                                                                                           |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| **Document ID** | A unique identifier assigned in the markdown frontmatter (e.g., `UN-001`, `REQ-003`). Displayed in monospace font throughout the UI. |
| **Title**       | The human-readable name of the Document, sourced from frontmatter.                                                                   |
| **Type**        | The classification of the Document. Displayed as a colored chip.                                                                     |
| **Status**      | The lifecycle state of a Document: Draft, In Review, Approved, or Obsolete.                                                          |
| **Last Synced** | The date when the Document was last updated via a repository sync operation.                                                         |
| **Refresh**     | Reloads the document list from the API without triggering a new sync from GitHub.                                                    |

## Document Types

| Internal Value         | Display Label        | Chip Color         |
| ---------------------- | -------------------- | ------------------ |
| `user_need`            | User Need            | Primary (blue)     |
| `requirement`          | Requirement          | Secondary (purple) |
| `architecture`         | Architecture         | Info (light blue)  |
| `detailed_design`      | Detailed Design      | Info (light blue)  |
| `test`                 | Test                 | Success (green)    |
| `sop`                  | SOP                  | Warning (orange)   |
| `work_instruction`     | Work Instruction     | Warning (orange)   |
| `policy`               | Policy               | Default (grey)     |
| `usability_risk`       | Usability Risk       | Error (red)        |
| `software_risk`        | Software Risk        | Error (red)        |
| `security_risk`        | Security Risk        | Error (red)        |
| `haz_soe_software`     | Software SoE         | Error (red)        |
| `haz_soe_security`     | Security SoE         | Error (red)        |
| `hazardous_situation`  | Hazardous Situation  | Warning (orange)   |
| `harm`                 | Harm                 | Error (red)        |
| `usability_plan`       | Usability Plan       | Info (light blue)  |
| `use_specification`    | Use Specification    | Info (light blue)  |
| `task_analysis`        | Task Analysis        | Info (light blue)  |
| `usability_evaluation` | Usability Evaluation | Info (light blue)  |

## Document Statuses

| Internal Value | Display Label | Chip Color      | Chip Variant |
| -------------- | ------------- | --------------- | ------------ |
| `draft`        | draft         | Default (grey)  | Outlined     |
| `in_review`    | in_review     | Primary (blue)  | Outlined     |
| `approved`     | approved      | Success (green) | Outlined     |
| `obsolete`     | obsolete      | Error (red)     | Outlined     |

## Link Types (Traceability)

| Internal Value | Display Label | Direction Context                   |
| -------------- | ------------- | ----------------------------------- |
| `derives_from` | Derives From  | Requirement derives from User Need  |
| `implements`   | Implements    | Architecture implements Requirement |
| `verified_by`  | Verified By   | Test verifies Requirement           |
| `mitigates`    | Mitigates     | Risk mitigation relationship        |
| `parent_of`    | Parent Of     | Hierarchical parent                 |
| `related_to`   | Related To    | General relationship                |
| `leads_to`     | Leads To      | Causal chain link                   |
| `results_in`   | Results In    | Outcome relationship                |
| `analyzes`     | Analyzes      | Analysis relationship               |

## Sidebar Sections

| Label                      | Definition                                                                        |
| -------------------------- | --------------------------------------------------------------------------------- |
| **Metadata**               | Card showing Document ID, Status, Type, Repository, Path, and Last Synced.        |
| **Upstream (linked from)** | Documents that reference the current Document. Shown with an upward arrow icon.   |
| **Downstream (links to)**  | Documents that the current Document references. Shown with a downward arrow icon. |
| **History**                | Changelog tab showing commit-level changes with release association.              |

## Traceability Matrix Terms

| Label                   | Definition                                                                                     |
| ----------------------- | ---------------------------------------------------------------------------------------------- |
| **Traceability Matrix** | Page title. A view showing which Documents have proper traceability links and which have gaps. |
| **Established Links**   | The link types that a Document already has in place.                                           |
| **Covered**             | A Document with all required traceability links established (green chip with checkmark).       |
| **Gap**                 | A Document missing one or more required traceability links (warning chip).                     |
| **N/A (top-level)**     | Displayed for Document types that have no required links (e.g., User Needs).                   |

## Change Types (Changelog)

| Internal Value | Display Label | Chip Color      |
| -------------- | ------------- | --------------- |
| `added`        | added         | Success (green) |
| `removed`      | removed       | Error (red)     |
| (other)        | (value)       | Default (grey)  |
