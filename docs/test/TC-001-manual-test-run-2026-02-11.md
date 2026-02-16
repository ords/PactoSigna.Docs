# Manual Test Run — 2026-02-11

**Environment:** https://qms.pactosigna.com
**Duration:** ~4 minutes (automated exploration)
**Pages explored:** 13
**Interactions performed:** ~30 (navigation, form validation, edge case testing)

## Findings

| #   | Type      | Severity | Page                 | Issue                                                                   |
| --- | --------- | -------- | -------------------- | ----------------------------------------------------------------------- |
| 1   | Bug       | medium   | `/settings`          | [#156](https://github.com/ords/PactoSigna.Experience.GitWeb/issues/156) |
| 2   | Bug       | low      | `/qms/documents/:id` | [#157](https://github.com/ords/PactoSigna.Experience.GitWeb/issues/157) |
| 3   | Ambiguous | --       | `/qms/traceability`  | [#158](https://github.com/ords/PactoSigna.Experience.GitWeb/issues/158) |

### Finding 1: Organization Settings frameworks always unchecked (#156)

All regulatory framework checkboxes (ISO 13485, IEC 62304, FDA 21 CFR Part 820) appear unchecked on the Settings page. Root cause: `useFetchOrganizations.ts:38` hard-codes `frameworks: []` when constructing Organization objects because the list API endpoint omits settings. Display-only bug — data is persisted correctly.

### Finding 2: Invalid document ID shows inline error without redirect (#157)

Navigating to `/qms/documents/NONEXISTENT-DOC-999` shows an inline error alert instead of a toast + redirect pattern (which was established in PR #154 for device/QMS IDs). Also generates 13 redundant 404 network requests from context re-renders.

### Finding 3: Traceability Matrix vs Dashboard gap count mismatch (#158)

Dashboard shows "14 Traceability Gaps" but the Traceability Matrix page shows "No Documents Found". Needs triage to determine if the gap count comes from another repository, or if the page filtering logic is too restrictive.

## Coverage

| Area                     |    Pages Visited     | Interactions | Findings |
| ------------------------ | :------------------: | :----------: | :------: |
| QMS Dashboard            |          1           |      1       |    0     |
| QMS Documents            | 4 (list + 3 details) |      5       |    0     |
| QMS Change Controls      |    1 (list only)     |      1       |    0     |
| QMS Signing Requests     |    1 (list only)     |      1       |    0     |
| QMS Traceability         |          1           |      1       |    1     |
| QMS Audit Log            |          1           |      1       |    0     |
| QMS Training             |          1           |      1       |    0     |
| Settings                 |          1           |      1       |    1     |
| Profile                  |          1           |      1       |    0     |
| Edge Cases (invalid IDs) |          1           |      1       |    1     |

## Limitations

- **No devices configured** on the sandbox — could not test device-scoped routes (device documents, releases, release detail, risk matrix, device traceability, device training).
- **No change controls, signing requests, or releases exist** — could only test list pages (empty states), not detail views or workflow transitions.
- **No training tasks assigned** — training page showed proper empty state but task workflows could not be tested.
- **Write path testing limited** — signing request "Send for Signing" button correctly disabled when form empty; change control create form not fully tested due to empty sandbox state.

## Observations

- **App health is generally good.** All QMS pages load without console errors or API failures on happy paths.
- **Document detail pages render beautifully.** Markdown content, metadata panel, links, and history tabs all work well (WI-001, WI-002, WI-003 tested).
- **Empty states are well-designed.** Change Controls, Signing Requests, and Training pages all have clear empty states with CTAs.
- **Audit log is functional** with proper data, filters, pagination, and CSV export button.
- **Performance is acceptable.** Dashboard ~4.8s, document list ~2.5s, document detail ~3.8-4.3s. Likely Cloud Function cold starts on sandbox.
- **Profile page displays correctly** with role (Admin), email verification badge, org switcher, and password reset.
- **Sidebar navigation is complete** with all expected items present.

## Backend Logs

No errors found in Firebase Cloud Function logs during the exploration window. All API calls returned 200 status codes for valid routes. The 404 for the invalid document ID was expected behavior.

---

_Found by manual-tester agent_
