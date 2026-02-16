---
id: SRS-310
title: 'Device Switcher'
status: draft
links:
  - type: derives-from
    target: PRS-008
---

# Device Switcher

## Requirement

> **SRS-310.1:** The system **SHALL** display a device switcher in the application header showing the current repository context, with a dropdown listing QMS and all device repositories (including device name and safety class).
>
> **SRS-310.2:** The system **SHALL** filter all document views to the selected repository when a device is selected.
>
> **SRS-310.3:** The system **SHALL** persist the selected repository across page navigation and reflect the selection in the URL for shareable links.

## Context

The device switcher provides context switching between the QMS repository and individual device repositories. The selected repository is stored in URL query parameters or path segments and in React Context for the current repository state. All document queries include a `repositoryId` filter.

## Classification

| Field           | Value               |
| --------------- | ------------------- |
| Safety Class    | A                   |
| Category        | Functional          |
| Bounded Context | Document Management |

## Detailed Specification

### Inputs

- User's repository selection from dropdown
- Available repositories for the organization

### Processing

1. Populate dropdown with QMS repository and all connected device repositories.
2. On selection, update URL and React Context.
3. All document queries filtered by selected `repositoryId`.

### Outputs

- Updated URL reflecting selected repository
- Filtered document views
- Persistent selection across navigation

### Error Handling

- No repositories connected: display prompt to connect a repository
- Selected repository disconnected: fall back to QMS repository

## Acceptance Criteria

1. **Given** a member viewing any page, **when** they open the device switcher, **then** QMS and all device repositories are listed with device names and safety classes.
2. **Given** a user selecting a device repository, **when** viewing document lists, **then** only documents from that repository are shown.
3. **Given** a selected device, **when** the user navigates to another page, **then** the selected repository persists.
4. **Given** a URL with a repository selection, **when** shared and opened by another member, **then** the same repository context is active.

## Traceability

| Direction | Link Type    | Target ID | Title                           |
| --------- | ------------ | --------- | ------------------------------- |
| Up        | derives-from | PRS-008    | Document linking & traceability |

## Source

**STORY-310 — Device Switcher**

> **As a** member **I want** to switch between QMS and device repositories **So that** I can focus on relevant documents.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
