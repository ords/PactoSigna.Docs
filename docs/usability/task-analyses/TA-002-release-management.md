# Release Management - User Journey

## Overview

Release Management enables Members to bundle changed Documents into a formal Release, collect electronic Signatures from required departments, and publish the Release as an auditable record. Releases follow a strict lifecycle: Draft, In Review, Approved, Published.

---

## Flow 1: Browse Releases

### Entry Point

Member navigates to the Releases page from a device context. The page title shows "{Device Name} Releases".

### Steps

1. **View the releases table** -- The system displays a table with columns: Name, Status, Documents (count), Signatures (progress bar), and Created date. Each row is clickable.
2. **Filter by status** -- The Member selects a status from the "Status" dropdown: Draft, In Review, Approved, or Published.
3. **Clear filters** -- If a filter is active, a "Clear" button appears.
4. **Refresh** -- The Member clicks "Refresh" to reload the releases list.
5. **Navigate to Create Release** -- The Member clicks "Create Release" to begin a new Release.

### Empty States

- **No releases, no filters** -- "No Releases Yet. Releases bundle documents together for review and signature. Create your first release to get started." with a "Create Release" button.
- **No releases, with filters** -- "No Releases Found. Try adjusting your filters to find releases." with a "Clear Filters" button.

### Signature Progress Column

Each row shows a horizontal progress bar with "{signed}/{required}" count. The bar turns green when all Signatures are collected (100%).

---

## Flow 2: Create a Release

### Entry Point

Member clicks "Create Release" from the Releases page. The URL becomes `/devices/{deviceId}/releases/new`.

### Steps

1. **Enter release details** -- The Member fills in:
   - **Release Name** (required) -- e.g., "v1.0.0 Initial Release"
   - **Description** (optional) -- Free text describing the purpose of the Release.

2. **Select required departments** -- The Member selects one or more departments from a multi-select dropdown. Departments are sourced from Organization Members plus defaults (Engineering, Quality, Regulatory, Clinical, Manufacturing). Each selected department appears as a chip. At least one department is required.

3. **Review auto-detected document changes** -- The system automatically detects all Documents that have changed since the last published Release. A summary shows counts by change type:
   - Added (green chip)
   - Modified (blue chip)
   - Removed (red chip)
   - The table shows: Change type, Title, Type, and Path for each Document.
   - If this is the first Release, all Documents are included with a message: "This is the first release - all documents will be included."
   - If no changes are detected: "No changes detected since the last published release. All documents are up to date."

4. **Submit** -- The Member clicks "Create Release". The system validates that: name is provided, at least one document change exists, and at least one department is selected. On success, the Member is redirected to the Release Detail page.

5. **Cancel** -- The Member clicks "Cancel" to return to the Releases list without creating.

---

## Flow 3: View Release Detail

### Entry Point

Member clicks a Release row in the releases table or is redirected after creating a Release.

### Steps

1. **View release header** -- The system displays:
   - Release name as heading
   - Type chip (QMS or Device)
   - Status chip (Draft, In Review, Approved, or Published)

2. **Read description** -- If a description was provided, it appears in a card below the header.

3. **Review included Documents** -- A "Documents ({count})" card lists all Documents in the Release. Each item shows: document title, document ID, document type, commit SHA, and a change type chip (added/modified/removed). Clicking a Document navigates to its detail page at the specific commit version.

4. **Review Release Traceability** -- A "Release Traceability" card shows:
   - **Coverage percentage** with a progress bar (e.g., "Coverage: 85%") and "{covered}/{total} linked" count.
   - A table with: Document ID, Type, Version (commit SHA), Links To (outgoing and incoming link chips), and Status (Linked or Isolated).
   - Clicking a row navigates to the Document at the release version.

5. **Review Risk Matrix** -- For non-published Releases, a "Current Risk Matrix" card shows live risk data:
   - Summary chips: Total, Acceptable (green), Review Required (warning), Unacceptable (red).
   - A warning alert if unacceptable risks exist.
   - For published Releases, a "Risk Matrix at Release" card shows the snapshot captured at publish time.

6. **View Gaps at Release** (published only) -- An expandable accordion shows gaps captured at publish time:
   - Summary chips showing error count and warning count.
   - A table with: Severity, Document ID/title, and Message.

7. **View Repository Links** -- A card with buttons to:
   - "View Repository at Release" -- Opens GitHub at the release commit.
   - "Compare with Previous Release" -- Opens GitHub compare view between previous and current release commits. Disabled if no previous Release exists.

8. **View metadata** -- A "Details" card shows: Created date, Submitted for Review date, Approved date, and Published date (as they become available through the lifecycle).

---

## Flow 4: Release Lifecycle Actions

### Draft Stage

- **Submit for Review** -- The Member clicks "Submit for Review" to transition the Release from Draft to In Review. This enables Signature collection.

### In Review Stage

- **Sign Release** -- Eligible Members see a "Sign Release" button in the sidebar (see Flow 5 below).
- **Withdraw** -- The Release creator can click "Withdraw" to return the Release to Draft status.

### Approved Stage

- **Publish** -- An admin clicks "Publish" to finalize the Release. This creates a snapshot of the risk matrix, traceability, and gaps data.

### Published Stage

- **Export for Auditors** -- The Member clicks "Export for Auditors" to request an export package. A snackbar confirms the request.

---

## Flow 5: Sign a Release (Electronic Signature)

### Entry Point

From the Release Detail page sidebar, an eligible Member clicks "Sign Release" to open the Signature Dialog.

### Prerequisite

The Member's department must be in the required departments list, and they must not have already signed.

### Steps

1. **Step 1: Verify identity** -- The dialog prompts the Member to enter their password. They type the password and click "Verify Identity" (or press Enter). On success, a green "Identity verified" alert appears.

2. **Step 2: Complete signature** -- After authentication, the Member:
   - Selects a **Signature Type**: Author, Reviewer, or Approver.
   - Selects a **Signature Meaning** from predefined options:
     - "I am the author of these changes"
     - "I have reviewed and approve these changes"
     - "I have verified compliance with requirements"
     - "Final approval granted"
     - Or "Custom" to enter a free-text meaning.
   - Checks the acknowledgment: "I understand this signature is legally binding and will be recorded with my identity and timestamp."

3. **Submit signature** -- The Member clicks "Sign Release". The dialog closes, and the sidebar updates to show the new Signature in the progress tracker.

### Signature Progress Sidebar

- A progress bar shows "{signed}/{required}" with percentage.
- Each required department is shown as a chip: green with checkmark if signed, grey with clock icon if pending.
- If the Member cannot sign, a reason is displayed (e.g., "You have already signed" or "Your department is not required").
- Below, the Signature Panel lists all collected Signatures with details.

### Compliance Note

This flow implements FDA 21 CFR Part 11 requirements for electronic signatures: re-authentication before signing, signature meaning selection, and legally binding acknowledgment.
