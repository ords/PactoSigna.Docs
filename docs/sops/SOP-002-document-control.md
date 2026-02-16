---
id: SOP-002
title: 'Document Control'
status: draft
links:
  - SOP-006
---

# Document Control

## 1. Purpose

Establish the procedure for creating, reviewing, approving, revising, and obsoleting controlled documents within the PactoSigna QMS, ensuring compliance with ISO 13485 §4.2.3 and §4.2.4.

## 2. Scope

Applies to all QMS documents managed in the PactoSigna repository, including SOPs, work instructions, requirements, architecture documents, risk artifacts, and test records. Applies to all team members who author or approve documents.

## 3. Definitions

| Term                | Definition                                                                |
| ------------------- | ------------------------------------------------------------------------- |
| Controlled Document | Any document managed under this procedure with YAML frontmatter and an ID |
| Frontmatter         | YAML metadata block at the top of each markdown file (id, title, status)  |
| Sync Engine         | PactoSigna component that imports markdown files from GitHub into the QMS |
| Document Lifecycle  | Draft → In Review → Effective → Obsolete                                  |

## 4. Responsibilities

| Role             | Responsibility                                                 |
| ---------------- | -------------------------------------------------------------- |
| Author           | Creates or revises the document, ensures frontmatter is valid  |
| Reviewer         | Reviews content for accuracy, completeness, and regulatory fit |
| Quality Manager  | Approves documents, ensures traceability, manages obsolescence |
| All Team Members | Use only effective versions; do not use obsolete documents     |

## 5. Procedure

### 5.1 Document Creation

1. Create a new markdown file in the appropriate `docs/` subdirectory using the relevant template from `docs/templates/`.
2. Assign a unique document ID following the naming convention (`{TYPE}-{NNN}`).
3. Complete the YAML frontmatter: `id`, `title`, `status: draft`, and `links` to related documents.
4. Commit the file to a feature branch via a GitHub Pull Request.

### 5.2 Review and Approval

1. Open a Pull Request with the new or revised document.
2. Assign at least one reviewer with domain knowledge of the content.
3. Reviewer checks: correctness, completeness, frontmatter validity, and cross-references.
4. Upon approval, the PR is merged to `main`. The merge constitutes formal approval.
5. The sync engine imports the document into PactoSigna, validating frontmatter on ingestion.

### 5.3 Revision

1. Create a feature branch and edit the document.
2. Update the Revision History table with a new version, date, author, and change summary.
3. Follow steps 5.2.1–5.2.5 for review and approval of the revision.
4. Git history provides a full audit trail of all changes.

### 5.4 Obsolescence

1. Change the document `status` field in frontmatter to `obsolete`.
2. Add a note at the top of the document body indicating the superseding document (if any).
3. Merge via PR following the standard review process.
4. The sync engine marks obsolete documents as inactive in the QMS.

## 6. Records

| Record               | Retention       | Location                    |
| -------------------- | --------------- | --------------------------- |
| Document files       | Life of product | GitHub repository (`docs/`) |
| Git commit history   | Life of product | GitHub                      |
| Pull Request records | Life of product | GitHub                      |
| Sync event logs      | 3 years         | Firestore (syncEvents)      |

## 7. References

- ISO 13485:2016 §4.2.3 — Control of Documents
- ISO 13485:2016 §4.2.4 — Control of Records
- SOP-006 — Change Control

## Revision History

| Version | Date       | Author        | Changes       |
| ------- | ---------- | ------------- | ------------- |
| 0.1     | 2026-02-15 | PactoSigna AI | Initial draft |
