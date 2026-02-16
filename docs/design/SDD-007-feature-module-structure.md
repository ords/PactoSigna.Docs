---
id: SDD-007
title: Feature Module Structure
status: Draft
links:
  source:
    - docs/architecture/SAD-003-module-boundaries.md
  related:
    - docs/architecture/SAD-003-module-boundaries.md
---

# SDD-007 Feature Module Structure

## Purpose

Standardize frontend feature folder structure and responsibilities.

## Module Layout

- `features/{name}/hooks`: viewmodel and query hooks.
- `features/{name}/components`: reusable presentational pieces.
- `features/{name}/pages`: route-level composition only.
- `features/{name}/context`: optional cross-page state container.
- `features/{name}/index.ts`: public exports.

## Design Rule

Keep business and data-fetching logic out of page components; pages orchestrate hooks and UI composition.

## Boundary Alignment

Structure aligns with bounded contexts and supports incremental migration from legacy patterns.
