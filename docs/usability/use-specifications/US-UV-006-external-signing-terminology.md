# External Signing - Terminology

## User-Facing Labels

| Label                      | Definition                                                                                                                   |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| **Signing Requests**       | Sidebar navigation item and page title. The list of all signing requests created by the Organization.                        |
| **New Signing Request**    | Button to create a new signing request.                                                                                      |
| **Export Release Package** | Button on published release detail page. Replaces the former "Export for Auditors" label. Independent from Signing Requests. |
| **Title**                  | Required field: the name of the signing request (e.g., "Q1 2026 Audit Report").                                              |
| **Description**            | Optional field: additional context for the signers.                                                                          |
| **Expiry Date**            | Required field: the date after which signing links become invalid. Maximum 30 days from creation.                            |

## Signer Labels

| Label                 | Definition                                                                                                                   |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| **Signer**            | An external party designated to sign. Identified by name and email. Does not need a PactoSigna account.                      |
| **Add Signer**        | Button to add another signer to the signing request.                                                                         |
| **Signature Meaning** | The statement the signer attests to when signing. Pre-defined options or custom text. Applies to all signers on the request. |

## Field Labels

| Label                      | Definition                                                                                                                                |
| -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| **Field**                  | A positioned marker on a document page that a signer must fill. Types: Signature, Initials, Date.                                         |
| **Signature**              | A full signature field. Signer draws or types their signature. Captured once per signing ceremony and reused across all signature fields. |
| **Initials**               | A 2-3 character initials field. Signer draws or types their initials. Captured once and reused across all initials fields.                |
| **Date**                   | An auto-populated date field. Shows the signing date. Server-validated at submission.                                                     |
| **Field Placement Editor** | The UI where the sender positions fields onto document pages before sending.                                                              |

## Document Labels

| Label               | Definition                                                                                                                                                          |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **SigningDocument** | A PDF uploaded as part of a Signing Request. Value object with no independent identity. NOT related to the Document Management bounded context's "Document" entity. |
| **Upload Document** | Button to add a PDF to the signing request.                                                                                                                         |

## Status Labels

### Signing Request Status

| Status               | Chip Color       | Definition                                                   |
| -------------------- | ---------------- | ------------------------------------------------------------ |
| **Sent**             | Info (blue)      | Signing invitation emails have been sent. No signatures yet. |
| **Partially Signed** | Warning (orange) | Some but not all signers have signed.                        |
| **Completed**        | Success (green)  | All signers have signed.                                     |
| **Expired**          | Error (red)      | Expiry date passed before all signatures were collected.     |
| **Cancelled**        | Default (grey)   | Member cancelled the signing request.                        |

### Signer Status

| Status       | Definition                                         |
| ------------ | -------------------------------------------------- |
| **Pending**  | Signer has not yet opened the signing link.        |
| **Viewed**   | Signer opened the signing link but has not signed. |
| **Signed**   | Signer completed the signing ceremony.             |
| **Declined** | Signer explicitly declined to sign.                |

## Signature Meaning Options

Pre-defined meanings for common use cases:

| Meaning                                          | Use Case                    |
| ------------------------------------------------ | --------------------------- |
| "I acknowledge receipt of this audit report"     | Audit report acknowledgment |
| "I have reviewed the enclosed documents"         | General document review     |
| "I agree to the terms of this quality agreement" | Quality/supplier agreements |
| Custom (free text)                               | Any other purpose           |

## Action Labels

| Label                   | Definition                                                                                                      |
| ----------------------- | --------------------------------------------------------------------------------------------------------------- |
| **Send**                | Button to send the signing request to all designated signers.                                                   |
| **Remind**              | Button to resend the signing invitation email to pending signers.                                               |
| **Cancel**              | Button to invalidate the signing request and all signing links.                                                 |
| **Download Signed PDF** | Button to download the completed PDF with signatures overlaid. Available only when status is "Completed".       |
| **Complete Signing**    | Button on the external signing page for the signer to submit all field values after email verification.         |
| **Verify Email**        | Prompt on the external signing page asking the signer to enter a 6-digit verification code sent to their email. |

## External Signing Page Labels

| Label                                                              | Definition                                                              |
| ------------------------------------------------------------------ | ----------------------------------------------------------------------- |
| "[Organization] has requested your signature"                      | Page heading shown to external signers.                                 |
| "Sign here"                                                        | Label on signature fields in the embedded PDF viewer.                   |
| "Initial here"                                                     | Label on initials fields in the embedded PDF viewer.                    |
| "Date"                                                             | Label on date fields in the embedded PDF viewer.                        |
| "By signing, you attest:"                                          | Section introducing the signature meaning on the summary step.          |
| "Verification code"                                                | Input field for the 6-digit email verification code.                    |
| "Send Code"                                                        | Button to send (or resend) the verification code to the signer's email. |
| "Draw your signature"                                              | Tab label for freehand signature canvas.                                |
| "Type your name"                                                   | Tab label for typed signature input.                                    |
| "Complete Signing"                                                 | Final submission button after email verification.                       |
| "This electronic signature is legally binding per 21 CFR Part 11." | Legal notice on the summary step.                                       |
| "This signing link is no longer valid"                             | Shown when a link is expired or cancelled.                              |
| "You have already signed this request"                             | Shown when a signer revisits after completing the ceremony.             |
