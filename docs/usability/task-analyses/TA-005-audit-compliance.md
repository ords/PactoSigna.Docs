# Audit & Compliance - User Journey

## Overview

The Audit Log provides a complete, tamper-evident record of all actions performed within the Organization. Every mutation in PactoSigna creates an audit entry, supporting FDA 21 CFR Part 11 compliance requirements for traceability and accountability.

---

## Flow 1: Browse the Audit Log

### Entry Point

Member navigates to the Audit Log page from the sidebar navigation.

### Steps

1. **View the audit log table** -- The system displays a compact table with columns:
   - Expand toggle (arrow icon)
   - **Timestamp** -- Formatted as "Mon DD, HH:MM:SS AM/PM"
   - **User** -- Email address of the Member who performed the action
   - **Action** -- Human-readable label (e.g., "Release Created", "Signature Added")
   - **Resource** -- Colored chip indicating the resource type (e.g., "release", "document", "member")
   - **Resource ID** -- Truncated Firestore ID (first 12 characters with ellipsis)

2. **Expand a row for details** -- Clicking a row toggles an expandable section showing the full JSON details of the audit entry in a formatted code block.

3. **Navigate pages** -- Below the table, "Previous" and "Next" buttons allow cursor-based pagination. The current page number is displayed between the buttons.

4. **Refresh** -- The Member clicks "Refresh" to reload the audit log from the beginning.

---

## Flow 2: Filter the Audit Log

### Entry Point

The Filters card is always visible above the audit log table.

### Steps

1. **Filter by date range** -- The Member sets a Start Date and/or End Date using date picker fields.
2. **Filter by action** -- The Member selects an action from the "Action" dropdown (e.g., "Release Created", "Member Invited", "Sync Triggered").
3. **Filter by user** -- The Member types an email address in the "User Email" field.
4. **Apply filters** -- The Member clicks the "Apply" button to execute the filtered query.
5. **Clear filters** -- The Member clicks "Clear" to reset all filters and reload unfiltered data.

---

## Flow 3: Export the Audit Log

### Entry Point

From the Audit Log page header, Member clicks "Export CSV".

### Steps

1. **Click Export CSV** -- The button is disabled when no entries are loaded. When entries exist, clicking triggers an immediate CSV download.
2. **Receive the file** -- The browser downloads a file named `audit-log-YYYY-MM-DD.csv` containing columns: Timestamp, User, Action, Resource Type, Resource ID, and Details (JSON stringified).

### Note

The export includes only the currently loaded page of entries. For a complete audit trail export, Members should work with the API directly or use paginated exports.

---

## Empty State

"No Audit Log Entries. Audit entries will appear here as actions are performed."

---

## Audit Actions Reference

The following actions are tracked and displayed with human-readable labels:

### Organization Actions

- Organization Created
- Settings Updated

### Member Actions

- Member Invited
- Member Joined
- Role Changed
- Member Removed

### Repository Actions

- Repository Connected
- Repository Disconnected
- Sync Triggered

### Document Actions

- Document Synced
- Document Removed

### Release Actions

- Release Created
- Release Updated
- Release Deleted
- Release Withdrawn
- Release Submitted
- Signature Added
- Release Approved
- Release Published

### Training Actions

- Training Assigned
- Training Completed
