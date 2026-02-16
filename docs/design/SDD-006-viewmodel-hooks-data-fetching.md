---
id: SDD-006
title: ViewModel Hooks Data Fetching
status: Draft
links:
  source:
    - docs/architecture/SAD-003-module-boundaries.md
  related:
    - docs/design/SDD-007-feature-module-structure.md
---

# SDD-006 ViewModel Hooks Data Fetching

## Purpose

Define the frontend data-fetching standard using React Query and feature hooks.

## Pattern

- Hook layer encapsulates query keys, fetch logic, loading/error composition, and mapping.
- Page layer composes hooks and renders presentational components.
- API types are consumed from contracts at the boundary.

## Benefits

- Reduced page complexity and duplicated state logic.
- Stable cache semantics and controlled fetch enablement.
- Clearer separation between transport, state, and UI concerns.

## Rule

For new frontend work, prefer hook-based ViewModel composition over ad hoc `useEffect` fetch flows.
