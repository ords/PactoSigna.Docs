---
id: HLD-001
title: System Context
status: Draft
links:
  related:
    - docs/architecture/HLD-002-deployment-runtime.md
    - docs/architecture/HLD-003-data-integration-flows.md
---

# HLD-001 System Context

## Purpose

Define the external systems, internal platform boundary, and primary responsibilities of each major runtime component.

## Context Summary

- User-facing system is a React SPA hosted on Firebase Hosting.
- Auth and primary data persistence are provided by Firebase Auth and Firestore.
- All write operations are performed through backend APIs and workers.
- GitHub provides repository content and webhook-triggered integration events.

## External Systems

- GitHub: repository content, app installation, and webhook events.
- Firebase Auth: identity provider and token issuance.
- Firestore: operational data store.
- Cloud Tasks and Cloud Scheduler: asynchronous and scheduled processing.
- Azure Graph: outbound email notifications.

## Internal Components

- `apps/web`: SPA and user workflow orchestration.
- `apps/services`: Fastify HTTP API (`servicesApi`) for validated and audited mutations.
- `apps/functions`: task workers and non-HTTP triggers.
- `packages/data`: Firestore repositories and persistence abstractions.
- `packages/contracts` and `packages/shared`: API DTO and shared domain logic boundaries.

## Architectural Constraints

- Frontend is read-oriented for Firestore; backend owns mutation flow.
- Cross-system actions requiring latency tolerance are queued.
- Domain boundaries follow documented bounded contexts.


---

## Technology Stack

## Monorepo

- **pnpm workspaces** — package management and workspace linking
- **Turborepo** — build orchestration and caching

## Frontend (`apps/web/`)

- **React 19** — UI library
- **Vite** — build tooling and dev server
- **MUI 6** — Material UI component library
- **React Query (TanStack Query)** — server state management
- **React Router 7** — client-side routing
- **TypeScript** — strict mode

## Backend (`apps/services/`, `apps/functions/`)

- **Firebase Cloud Functions** (Node 20) — serverless compute
- **Fastify** — HTTP framework (apps/services)
- **Firestore** — NoSQL document database
- **Google Cloud Tasks** — async background processing
- **Google Cloud Scheduler** — CRON jobs

## Shared Packages

- **@pactosigna/shared** — domain logic: validators, enums, predicates, calculations
- **@pactosigna/contracts** — API request/response DTOs (Zod schemas)
- **@pactosigna/data** — Firestore repositories, models, email

## Testing

- **Vitest** — unit testing
- **Playwright + playwright-bdd** — E2E testing with Gherkin syntax
- **Firebase Emulator Suite** — local auth, firestore, functions

## Validation

- **Zod** — runtime schema validation, shared between frontend and backend
