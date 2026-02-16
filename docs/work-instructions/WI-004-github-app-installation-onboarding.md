# GitHub App Installation Onboarding: Backend Implementation Plan

## Overview

This document describes the required backend logic and workflow for handling `installation.created` events from the GitHub App webhook, in order to meet the onboarding and repository connection requirements described in Epic 2.

---

## 1. Event Handling: `installation.created`

- **Trigger:** GitHub App sends an `installation` event with `action: 'created'` to the webhook endpoint.
- **Required Action:**
  - Extract `installation.id` and organization/account info from the event payload.
  - Map the GitHub org/account (by login or external ID) to a Firestore organization document.
  - Update the organization document:
    - Set `gitHubAppInstallationId` to the installation ID.
    - Set `gitHubOrgName` to the GitHub org/account name.
  - Log the onboarding event and any errors.

## 2. Onboarding Flow Trigger

- **After Firestore update:**
  - Ensure the frontend can detect the new installation and prompt the user to select repositories.
  - Optionally, set an onboarding status field or emit a domain event to drive the UI.

## 3. Repository Listing and Selection

- **Backend responsibilities:**
  - Use the GitHub API (with the installation token) to list accessible repositories for the org.
  - Expose a callable function or endpoint for the frontend to fetch this list.
  - For each repository selected by the user:
    - Create a Firestore document under `/organizations/{orgId}/repositories/{repoId}`.
    - Store repository metadata (type, device info, etc.).

## 4. Initial Sync

- **For each connected repository:**
  - Trigger the initial sync using a Cloud Function (e.g., `triggerRepositorySync`).
  - Log sync status and events in Firestore.

## 5. Logging and Auditing

- **Best practices:**
  - Log all onboarding steps and errors for traceability.
  - Create an audit log entry for `repository.connected` or similar.

## 6. Error Handling & Idempotency

- Ensure onboarding logic is idempotent (safe to retry if the event is delivered multiple times).
- Handle cases where the org mapping is ambiguous or missing (log and alert).

---

## Acceptance Criteria

- [ ] Organization document is updated with installation ID and org name on install.
- [ ] Frontend can detect onboarding state and prompt repository selection.
- [ ] Backend exposes repository listing and selection endpoints.
- [ ] Initial sync is triggered for each connected repository.
- [ ] All steps are logged and auditable.
- [ ] Logic is idempotent and robust to errors.

---

## References

- [PRS-004: GitHub App Integration](../product-requirements/PRS-004-github-app-integration.md)
- [GitHub Webhook Events](https://docs.github.com/en/developers/webhooks-and-events/webhooks/webhook-events-and-payloads#installation)
- [GitHub App Installation API](https://docs.github.com/en/rest/apps/installations)
