---
id: SDD-008
title: Routing and Navigation Guards
status: Draft
links:
  source:
    - docs/architecture/SAD-003-module-boundaries.md
  related:
    - docs/design/SDD-005-app-shell-provider-chain.md
---

# SDD-008 Routing and Navigation Guards

## Purpose

Define route gating and navigation guard behavior across authenticated workflows.

## Guard Inputs

- Authentication readiness and signed-in state.
- Organization availability and active selection.
- Repository/device context derived from route parameters when applicable.

## Guard Behavior

- Hold navigation while required parent contexts are loading.
- Redirect unauthenticated users to auth entry routes.
- Redirect authenticated users without organizations to organization setup.
- Resolve device-scoped URLs into repository selection before rendering dependent pages.

## Outcome

Guards prevent invalid page states and ensure consistent navigation semantics.
