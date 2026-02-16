---
id: HLD-002
title: Deployment Runtime
status: Draft
links:
  related:
    - docs/architecture/HLD-001-system-context.md
    - docs/architecture/SAD-003-module-boundaries.md
---

# HLD-002 Deployment Runtime

## Purpose

Specify the deployment targets, runtime responsibilities, and route-to-runtime mapping.

## Runtime Topology

- Firebase Hosting serves SPA and API entrypoints.
- `servicesApi` (Node 20) hosts Fastify routes for `/api/**` except explicit task routes.
- Dedicated Cloud Functions handle task worker endpoints and scheduled jobs.
- Firestore and Auth provide managed persistence and identity.

## Routing Summary

- `/api/**` → `servicesApi`.
- `/api/tasks/export-worker` → `exportWorker`.
- `/api/tasks/training-assignment` → `trainingAssignmentWorker`.
- Non-API routes fall back to SPA hosting target.

## Environment Notes

- Emulator ports and local topology mirror production services where possible.
- Cloud Tasks behavior differs locally; queue behavior may be mocked or inlined.

## Operational Constraints

- Runtime split isolates synchronous API from long-running workloads.
- Scheduled and queue workers must be idempotent and auditable.
