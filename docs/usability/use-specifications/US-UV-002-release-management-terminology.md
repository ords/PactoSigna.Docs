# Release Management - Terminology

## User-Facing Labels

| Label                    | Definition                                                                                                              |
| ------------------------ | ----------------------------------------------------------------------------------------------------------------------- |
| **Release**              | A formal bundle of changed Documents, submitted for review and Signature collection. Not called "version" or "package". |
| **Create Release**       | Action button to start a new Release for a device.                                                                      |
| **Release Name**         | User-provided name for the Release (e.g., "v1.0.0 Initial Release").                                                    |
| **Description**          | Optional free-text field describing the purpose of the Release.                                                         |
| **Required Departments** | Departments that must provide a Signature before the Release can be approved.                                           |
| **Documents to Release** | Auto-detected list of Documents that changed since the last published Release.                                          |
| **Signature Progress**   | Visual indicator (progress bar + count) showing how many required departments have signed.                              |

## Release Statuses

| Internal Value | Display Label | Chip Color        | Description                              |
| -------------- | ------------- | ----------------- | ---------------------------------------- |
| `draft`        | Draft         | Default (grey)    | Initial state. Release can be edited.    |
| `in_review`    | In Review     | Primary (blue)    | Submitted for Signature collection.      |
| `approved`     | Approved      | Success (green)   | All required Signatures collected.       |
| `published`    | Published     | Info (light blue) | Finalized with snapshot data. Immutable. |

## Release Actions

| Button Label            | Availability                       | Description                                                      |
| ----------------------- | ---------------------------------- | ---------------------------------------------------------------- |
| **Submit for Review**   | Draft status                       | Transitions Release to In Review, enabling Signature collection. |
| **Withdraw**            | In Review status                   | Returns Release to Draft status.                                 |
| **Publish**             | Approved status, admin only        | Finalizes the Release with a snapshot.                           |
| **Export for Auditors** | Published status                   | Requests an export package for audit purposes.                   |
| **Sign Release**        | In Review status, eligible Members | Opens the Signature dialog.                                      |

## Signature Dialog Labels

| Label                               | Definition                                                                        |
| ----------------------------------- | --------------------------------------------------------------------------------- |
| **Step 1: Verify Your Identity**    | Re-authentication step requiring the Member's password.                           |
| **Verify Identity**                 | Button to submit password for re-authentication.                                  |
| **Identity verified**               | Success message shown after successful re-authentication.                         |
| **Step 2: Complete Your Signature** | Signature details step shown after authentication.                                |
| **Signature Type**                  | The role of the signer: Author, Reviewer, or Approver.                            |
| **Signature Meaning**               | The intent behind the Signature, selected from predefined options or custom text. |
| **Sign Release**                    | Final action button in the dialog to submit the Signature.                        |

## Predefined Signature Meanings

| Meaning                                      |
| -------------------------------------------- |
| I am the author of these changes             |
| I have reviewed and approve these changes    |
| I have verified compliance with requirements |
| Final approval granted                       |

## Signature Types

| Internal Value | Display Label |
| -------------- | ------------- |
| `author`       | Author        |
| `reviewer`     | Reviewer      |
| `approver`     | Approver      |

## Document Change Types

| Internal Value | Display Label | Chip Color      | Icon        |
| -------------- | ------------- | --------------- | ----------- |
| `added`        | Added         | Success (green) | Plus icon   |
| `modified`     | Modified      | Info (blue)     | Edit icon   |
| `removed`      | Removed       | Error (red)     | Delete icon |

## Release Detail Sections

| Section                    | Description                                                                                   |
| -------------------------- | --------------------------------------------------------------------------------------------- |
| **Documents ({count})**    | List of all Documents included in the Release with change types and commit versions.          |
| **Release Traceability**   | Table showing traceability links between Documents in the Release, with coverage percentage.  |
| **Current Risk Matrix**    | Live risk data for non-published Releases (Total, Acceptable, Review Required, Unacceptable). |
| **Risk Matrix at Release** | Snapshot of risk data captured at publish time for published Releases.                        |
| **Gaps at Release**        | Expandable accordion showing gaps (errors and warnings) captured at publish time.             |
| **Signature Progress**     | Sidebar card with progress bar and department-level chip indicators.                          |
| **Details**                | Sidebar card with lifecycle timestamps (Created, Submitted, Approved, Published).             |
| **Repository**             | Sidebar card with links to view the repository on GitHub or compare with previous Release.    |

## Traceability Terms in Release Context

| Label        | Definition                                                                                      |
| ------------ | ----------------------------------------------------------------------------------------------- |
| **Coverage** | Percentage of Documents in the Release that have at least one traceability link.                |
| **Linked**   | A Document with at least one traceability link to another Document in the Release (green chip). |
| **Isolated** | A Document with no traceability links to other Documents in the Release (warning chip).         |

## Gap Severities

| Internal Value | Display Label | Chip Color       |
| -------------- | ------------- | ---------------- |
| `error`        | error         | Error (red)      |
| `warning`      | warning       | Warning (orange) |
