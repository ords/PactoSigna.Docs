# Organization Setup - Terminology

## User-Facing Labels

| Label                     | Definition                                                                                                    |
| ------------------------- | ------------------------------------------------------------------------------------------------------------- |
| **Organization**          | The top-level entity in PactoSigna. Not called Company, Tenant, or Account.                                   |
| **Organization Settings** | Admin page for managing the Organization's name, frameworks, departments, Members, invites, and Repositories. |
| **Organization Name**     | The display name of the Organization (3-100 characters).                                                      |

## Tabs

| Tab Label           | Description                                                            |
| ------------------- | ---------------------------------------------------------------------- |
| **General**         | Organization name and regulatory framework selection.                  |
| **Departments**     | Manage default and custom departments.                                 |
| **Members**         | View, search, edit, and remove team Members.                           |
| **Pending Invites** | View, resend, and revoke outstanding invitations.                      |
| **Repositories**    | Manage GitHub Repository connections (delegated to Repositories page). |

## Regulatory Frameworks

| Framework          | Description                                           |
| ------------------ | ----------------------------------------------------- |
| **ISO 13485**      | Quality management systems for medical devices.       |
| **IEC 62304**      | Medical device software lifecycle processes.          |
| **FDA 21 CFR 820** | US FDA Quality System Regulation for medical devices. |

## Member Roles

| Role        | Chip Color     | Description                                                              |
| ----------- | -------------- | ------------------------------------------------------------------------ |
| **Admin**   | Error (red)    | Full access to Organization settings, publishing, and Member management. |
| **Member**  | Primary (blue) | Standard access to documents, Releases, and Signature capabilities.      |
| **Auditor** | Default (grey) | Read-only access for compliance auditing purposes.                       |

## Department Labels

### Default Departments

Engineering, Quality, Regulatory, Clinical, Manufacturing.

### Custom Departments

Displayed as outlined primary chips with a delete icon. Added via a dialog.

## Invite Labels

| Label               | Definition                                                      |
| ------------------- | --------------------------------------------------------------- |
| **Invite Member**   | Button to open the invitation dialog.                           |
| **Email Address**   | The email to send the invitation to.                            |
| **Department**      | The department the invited Member will be assigned to.          |
| **Role**            | The role (Admin, Member, Auditor) the invited Member will have. |
| **Send Invitation** | Submit button in the invite dialog.                             |
| **Expired**         | Red chip shown for invitations past their expiration date.      |
| **Resend**          | Action to resend an invitation email (refresh icon).            |
| **Revoke**          | Action to cancel an outstanding invitation (cancel icon).       |

## Repository Labels

| Label                  | Definition                                                                    |
| ---------------------- | ----------------------------------------------------------------------------- |
| **Repository**         | A GitHub repository connected to the Organization. Not abbreviated as "repo". |
| **Connect GitHub**     | Button to start the GitHub App installation flow.                             |
| **Connect Repository** | Button to link an available GitHub Repository to the Organization.            |
| **GitHub Connection**  | Card showing whether the GitHub App is installed for the Organization.        |
| **Connected**          | Green chip indicating the GitHub App is installed.                            |
| **Not Connected**      | Grey chip indicating no GitHub App is installed.                              |

## Repository Properties

| Label               | Definition                                                                              |
| ------------------- | --------------------------------------------------------------------------------------- |
| **Repository Type** | Either "QMS Repository" (one per Organization) or "Device Repository" (one per device). |
| **Device Name**     | The name of the medical device associated with a device Repository.                     |
| **Safety Class**    | IEC 62304 software safety classification: Class A, B, or C.                             |
| **Sync Status**     | The state of document synchronization: active (green), paused (orange), or error (red). |
| **Last Synced**     | Date of the most recent successful sync from GitHub. Shows "Never" if not yet synced.   |

## Safety Classes

| Class | Label                                      | Description                                                 |
| ----- | ------------------------------------------ | ----------------------------------------------------------- |
| **A** | Class A - No injury possible               | Software where failure cannot lead to injury.               |
| **B** | Class B - Non-serious injury possible      | Software where failure can lead to non-serious injury.      |
| **C** | Class C - Death or serious injury possible | Software where failure can lead to death or serious injury. |

## Repository Actions

| Icon       | Label        | Description                                                              |
| ---------- | ------------ | ------------------------------------------------------------------------ |
| Sparkle    | How to Adapt | Opens a panel explaining how to structure the repository for PactoSigna. |
| Sync       | Trigger Sync | Manually triggers a document sync from GitHub.                           |
| Disconnect | Disconnect   | Removes the Repository connection (with confirmation).                   |

## Profile Labels

| Label                   | Definition                                                              |
| ----------------------- | ----------------------------------------------------------------------- |
| **My Profile**          | Page showing the Member's personal information and settings.            |
| **Display Name**        | The name shown to other Members in the Organization (2-100 characters). |
| **Email Verified**      | Green chip indicating the Member's email has been verified.             |
| **Email Not Verified**  | Orange chip indicating the Member's email has not been verified.        |
| **Reset Password**      | Button to send a password reset email.                                  |
| **Active Organization** | Card showing which Organization the Member is currently working in.     |
| **Switch**              | Button to change the active Organization context.                       |
