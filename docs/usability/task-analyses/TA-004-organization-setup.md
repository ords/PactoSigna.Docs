# Organization Setup - User Journey

## Overview

Organization Setup covers the initial configuration and ongoing management of an Organization in PactoSigna. This includes managing general settings, departments, team Members, invitations, and GitHub Repository connections.

---

## Flow 1: Manage General Settings

### Entry Point

Member (admin) navigates to Organization Settings from the sidebar.

### Steps

1. **View the settings page** -- The system displays "Organization Settings" with tabs: General, Departments, Members, Pending Invites, and Repositories.
2. **Edit organization name** -- The Member types a new name in the "Organization Name" field (3-100 characters required).
3. **Select regulatory frameworks** -- The Member checks one or more frameworks:
   - ISO 13485
   - IEC 62304
   - FDA 21 CFR 820
     At least one framework must be selected.
4. **Save changes** -- The Member clicks "Save Changes". The button is disabled if no changes were made. A success or error alert appears.

---

## Flow 2: Manage Departments

### Entry Point

From Organization Settings, Member clicks the "Departments" tab.

### Steps

1. **View departments** -- The system displays default departments as grey chips and custom departments as outlined primary chips with delete buttons.
2. **Add a custom department** -- The Member clicks "Add Department", types a name in the dialog, and clicks "Add" (or presses Enter).
3. **Remove a custom department** -- The Member clicks the delete icon on a custom department chip. Default departments cannot be removed.

### Default Departments

Engineering, Quality, Regulatory, Clinical, Manufacturing.

---

## Flow 3: Manage Members

### Entry Point

From Organization Settings, Member clicks the "Members" tab.

### Steps

1. **View the members table** -- The system displays a table with columns: Member (avatar, name, email), Department, Role (colored chip), Joined date, and Actions (edit, remove).
2. **Search members** -- The Member types in the "Search members..." field to filter the table.
3. **Edit a member** -- The Member clicks the edit icon to open a dialog where they can change the member's department and role (Admin, Member, or Auditor). Clicking "Save Changes" applies the update.
4. **Remove a member** -- The Member clicks the delete icon. The last remaining admin cannot be removed (button is disabled).

### Role Colors

| Role    | Chip Color     |
| ------- | -------------- |
| Admin   | Error (red)    |
| Member  | Primary (blue) |
| Auditor | Default (grey) |

---

## Flow 4: Invite a Member

### Entry Point

From the Members tab, Member clicks "Invite Member" to open the invite dialog.

### Steps

1. **Enter invite details** -- The Member fills in:
   - **Email Address** (required, validated as email format)
   - **Department** (required, selected from default departments)
   - **Role** (required, defaults to "member"): Admin, Member, or Auditor
2. **Send invitation** -- The Member clicks "Send Invitation". On success, the dialog closes.
3. **View pending invites** -- The Member switches to the "Pending Invites" tab to see outstanding invitations.

---

## Flow 5: Manage Pending Invites

### Entry Point

From Organization Settings, Member clicks the "Pending Invites" tab.

### Steps

1. **View pending invitations** -- The system displays a table with columns: Email, Department, Role, Invited date, Expires date, and Actions.
2. **Check expiration** -- Expired invitations show a red "Expired" chip instead of a date.
3. **Resend an invite** -- The Member clicks the refresh icon to resend the invitation email.
4. **Revoke an invite** -- The Member clicks the cancel icon to revoke the invitation.

### Empty State

"No pending invitations" with a person icon.

---

## Flow 6: Connect a GitHub Repository

### Entry Point

Member navigates to the Repositories page from the sidebar or from the "Repositories" tab in Organization Settings.

### Steps

1. **View GitHub connection status** -- A card shows the GitHub connection state:
   - **Connected** (green chip) -- "Connected to {org name}" with available actions.
   - **Not Connected** (grey chip) -- "Connect your GitHub organization to sync repositories" with a "Connect GitHub" button.

2. **Install the GitHub App** (first time) -- The Member clicks "Connect GitHub". The browser redirects to GitHub's App installation flow. After installing, the Member is redirected back with an `installation_id` parameter, and the system completes the connection.

3. **Connect a repository** -- The Member clicks "Connect Repository" to open a dialog:
   - **Select Repository** -- Dropdown showing available GitHub repositories not yet connected.
   - **Repository Type** -- "QMS Repository (one per org)" or "Device Repository".
   - If Device type is selected:
     - **Device Name** (required) -- e.g., "CardioMonitor Pro".
     - **Safety Class** -- Class A (No injury possible), Class B (Non-serious injury possible), or Class C (Death or serious injury possible).
   - The Member clicks "Connect" to establish the connection.

4. **View connected repositories** -- A table shows: Repository name, Type (chip), Device, Safety Class, Sync Status (chip: active/paused/error), Last Synced date, and Actions.

5. **Trigger a sync** -- The Member clicks the sync icon to manually trigger a document sync from GitHub.

6. **View How to Adapt guide** -- The Member clicks the sparkle icon to open a panel with instructions on how to structure their repository for PactoSigna.

7. **Disconnect a repository** -- The Member clicks the disconnect icon. A confirmation dialog appears before disconnecting.

8. **Refresh** -- The Member clicks "Refresh" to reload the repository list.

### Empty State

A dedicated empty state component is shown when no repositories are connected, with guidance on connecting GitHub or adding a repository.

---

## Flow 7: User Profile

### Entry Point

Member navigates to the Profile page from the user menu.

### Steps

1. **View profile overview** -- A card shows the Member's avatar (first letter), display name, email, and email verification status (green "Email Verified" chip or orange "Email Not Verified" chip).

2. **Edit display name** -- The Member types a new name (2-100 characters) and clicks "Save Changes".

3. **Reset password** -- In the Security card, the Member clicks "Reset Password". A password reset email is sent.

4. **Switch Organization** -- In the Active Organization card, the Member sees all Organizations they belong to. Clicking "Switch" on another Organization changes the active context and navigates to the dashboard.
