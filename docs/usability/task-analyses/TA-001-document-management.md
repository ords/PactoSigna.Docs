# Document Management - User Journey

## Overview

Document Management is the core feature of PactoSigna. It enables Members to browse, search, and inspect quality management documents that are synced from GitHub repositories. Documents are parsed from markdown files with frontmatter metadata.

---

## Flow 1: Browse Documents

### Entry Point

Member navigates to the Documents page via the sidebar navigation. The page title reflects the repository context ("QMS Documents" or "{Device Name} Documents").

### Steps

1. **View the documents table** -- The system displays a paginated table with columns: Document ID, Title, Type, Status, and Last Synced. Each row is clickable.
2. **Filter by document type** -- The Member selects a type from the "Type" dropdown (e.g., User Need, Requirement, Architecture, Test, SOP). The table updates immediately.
3. **Filter by status** -- The Member selects a status from the "Status" dropdown: Draft, In Review, Approved, or Obsolete. The table updates immediately.
4. **Search by ID or title** -- The Member types a query into the "Search by ID or title..." text field and presses Enter or clicks the "Search" button.
5. **Clear filters** -- When any filters are active, a "Clear" button appears. Clicking it resets all filters and the search query.
6. **Navigate pages** -- If the document count exceeds 20, pagination controls appear below the table. The Member clicks page numbers to browse.
7. **Refresh** -- The Member clicks the "Refresh" button in the header to reload documents from the API.

### Empty States

- **No documents, no filters** -- "No Documents Found. Documents will appear here once the repository is synced."
- **No documents, with filters** -- "No Documents Found. Try adjusting your filters or search query." with a "Clear Filters" button.

---

## Flow 2: View Document Detail

### Entry Point

Member clicks a document row in the documents table. The URL becomes `/documents/{documentId}` (using the frontmatter Document ID, not the Firestore ID).

### Steps

1. **View document header** -- The system displays: document ID and title as heading, a Type chip (e.g., "Requirement"), a Status chip (e.g., "approved"), and the current commit SHA (truncated to 7 characters).
2. **Read document content** -- The main content area renders the markdown document with support for headings, paragraphs, lists, code blocks, and GFM tables.
3. **View metadata sidebar** -- A sidebar card shows: Document ID, Status, Type, Repository name, File path, and Last Synced date.
4. **View traceability links** -- The "Links" tab in the sidebar shows:
   - **Upstream (linked from)** -- Documents that reference this document, each with a link type label (e.g., "Derives From").
   - **Downstream (links to)** -- Documents this document references, each with a link type label.
   - Clicking any link navigates to the linked document's detail page.
5. **View on GitHub** -- Clicking "View on GitHub" opens the source markdown file on GitHub in a new tab.
6. **Navigate back** -- Clicking "Back to Documents" returns to the documents list.

---

## Flow 3: View Document History (Changelog)

### Entry Point

From the Document Detail page, Member clicks the "History" tab in the sidebar.

### Steps

1. **View changelog entries** -- The system displays up to 10 changelog entries. Each entry shows:
   - Commit SHA (truncated to 7 characters)
   - Change type chip: "added" (green) or "removed" (red), or "modified" (default)
   - Release association: a clickable chip with the release name if released, or an "Unreleased" chip if not
   - Author email and date
2. **View a historical version** -- The Member clicks the "View" button on a changelog entry. The document content updates to show that version. A warning banner appears: "You are viewing a historical version ({sha}) of this document. The current version may be different."
3. **Return to latest version** -- The Member clicks "View latest version" on the warning banner to return to the current document content.

---

## Flow 4: View Traceability Matrix

### Entry Point

Member navigates to the Traceability Matrix page via the sidebar navigation.

### Steps

1. **View the gap report** -- The system displays a table with columns: Document ID, Document Title, Established Links, and Status.
2. **Review gap summary** -- If gaps exist, a warning alert shows: "{N} documents have missing traceability links and need attention."
3. **Filter by document type** -- The Member selects a document type from the "Document Type" dropdown to narrow the view.
4. **Interpret status indicators** -- Each row shows one of:
   - **Covered** (green chip with checkmark) -- All required traceability links are established.
   - **Gap** (warning chip) -- Missing required links. Hovering shows a tooltip listing the missing link types (e.g., "Missing: Derives From").
5. **Review established links** -- The "Established Links" column shows which link types are already in place for each document (e.g., "Implements, Verified By") or "N/A (top-level)" for User Needs.

### Expected Links by Document Type

- **User Need** -- No required links (top-level documents).
- **Requirement** -- Should have "Derives From" links to User Needs.
- **Architecture** -- Should have "Implements" links to Requirements.
- **Detailed Design** -- Should have "Implements" links to Architecture.
- **Test** -- Should have "Verified By" links to Requirements.
