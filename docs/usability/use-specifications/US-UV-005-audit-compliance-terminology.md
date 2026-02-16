# Audit & Compliance - Terminology

## User-Facing Labels

| Label           | Definition                                                                             |
| --------------- | -------------------------------------------------------------------------------------- |
| **Audit Log**   | Page title. A chronological record of all actions performed within the Organization.   |
| **Timestamp**   | The date and time an action was recorded, formatted as "Mon DD, HH:MM:SS AM/PM".       |
| **User**        | The email address of the Member who performed the action.                              |
| **Action**      | A human-readable description of what was done (e.g., "Release Created").               |
| **Resource**    | The type of entity affected by the action, shown as a colored chip.                    |
| **Resource ID** | The unique identifier of the affected entity, truncated to 12 characters in the table. |
| **Details**     | Expandable JSON object containing the full context of the audit entry.                 |

## Filter Controls

| Label          | Definition                                                     |
| -------------- | -------------------------------------------------------------- |
| **Filters**    | Section title for the filter card.                             |
| **Start Date** | Date picker to set the beginning of the query range.           |
| **End Date**   | Date picker to set the end of the query range.                 |
| **Action**     | Dropdown to filter by a specific audit action type.            |
| **User Email** | Text field to filter entries by the performing Member's email. |
| **Apply**      | Button to execute the current filter configuration.            |
| **Clear**      | Button to reset all filters to their defaults.                 |
| **Refresh**    | Button to reload the audit log from the beginning.             |

## Resource Type Colors

| Resource Type  | Chip Color         |
| -------------- | ------------------ |
| `organization` | Primary (blue)     |
| `member`       | Secondary (purple) |
| `invite`       | Secondary (purple) |
| `repository`   | Info (light blue)  |
| `document`     | Success (green)    |
| `release`      | Warning (orange)   |
| `signature`    | Warning (orange)   |
| `training`     | Error (red)        |

## Audit Actions

### Organization

| Internal Value                  | Display Label        |
| ------------------------------- | -------------------- |
| `organization.created`          | Organization Created |
| `organization.settings_updated` | Settings Updated     |

### Member

| Internal Value        | Display Label  |
| --------------------- | -------------- |
| `member.invited`      | Member Invited |
| `member.joined`       | Member Joined  |
| `member.role_changed` | Role Changed   |
| `member.removed`      | Member Removed |

### Repository

| Internal Value              | Display Label           |
| --------------------------- | ----------------------- |
| `repository.connected`      | Repository Connected    |
| `repository.disconnected`   | Repository Disconnected |
| `repository.sync_triggered` | Sync Triggered          |

### Document

| Internal Value     | Display Label    |
| ------------------ | ---------------- |
| `document.synced`  | Document Synced  |
| `document.removed` | Document Removed |

### Release

| Internal Value                 | Display Label     |
| ------------------------------ | ----------------- |
| `release.created`              | Release Created   |
| `release.updated`              | Release Updated   |
| `release.deleted`              | Release Deleted   |
| `release.withdrawn`            | Release Withdrawn |
| `release.submitted_for_review` | Release Submitted |
| `release.signature_added`      | Signature Added   |
| `release.approved`             | Release Approved  |
| `release.published`            | Release Published |

### Training

| Internal Value       | Display Label      |
| -------------------- | ------------------ |
| `training.assigned`  | Training Assigned  |
| `training.completed` | Training Completed |

## Export Labels

| Label                        | Definition                                                                                               |
| ---------------------------- | -------------------------------------------------------------------------------------------------------- |
| **Export CSV**               | Button to download the current page of audit entries as a CSV file. Disabled when no entries are loaded. |
| **audit-log-YYYY-MM-DD.csv** | The naming convention for exported CSV files, using the current date.                                    |

## Pagination

| Label        | Definition                                                                 |
| ------------ | -------------------------------------------------------------------------- |
| **Previous** | Navigate to the previous page of results. Disabled on page 1.              |
| **Next**     | Navigate to the next page of results. Disabled when no more entries exist. |
| **Page {N}** | Current page number indicator.                                             |
