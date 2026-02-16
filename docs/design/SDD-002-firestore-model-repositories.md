---
id: SDD-002
title: Firestore Model and Repositories
status: Draft
links:
  source:
    - docs/architecture/HLD-003-data-integration-flows.md
  related:
    - docs/architecture/HLD-003-data-integration-flows.md
---

# SDD-002 Firestore Model and Repositories

## Purpose

Define Firestore collection layout and repository responsibilities.

## Data Model Highlights

- Organization-scoped collections store most bounded-context entities.
- Repository-nested documents support document lifecycle and sync patterns.
- Audit and signature records require immutable semantics.

## Repository Responsibilities

- Encapsulate query composition and persistence mapping.
- Normalize timestamp conversion across read/write paths.
- Guard against `undefined` writes and shape drift.

## Index Policy

- Any query requiring composite indexes must be captured in `firebase/firestore.indexes.json`.
- Emulator behavior is insufficient for index validation; production parity is required in review.


---

## Backend Architecture Patterns

Full design: See ARCH docs ([HLD-001](../architecture/HLD-001-system-context.md) through [SAD-003](../architecture/SAD-003-module-boundaries.md)) and DD docs ([SDD-001](../design/SDD-001-fastify-modules.md) through [SDD-004](../design/SDD-004-auth-and-access-control.md)).

## Backend Structure

- **apps/services** — All HTTP APIs (servicesApi Cloud Function)
  - Fastify server with Zod validation
  - Routes: /api/v2/_, /api/github/_
  - Modules: identity, repositories, documents, releases, etc.

- **apps/functions** — Non-HTTP Cloud Functions
  - onBeforeUserCreated — Auth trigger
  - syncWorker — Document sync from GitHub (Cloud Tasks)
  - exportWorker — PDF export processing (Cloud Tasks)
  - trainingAssignmentWorker — Auto-assign training on release publish (Cloud Tasks)
  - trainingOverdueWorker — Daily CRON for overdue training notifications (Cloud Scheduler)
  - waitlistApi — Landing page API (separate hosting target)

## API Routing (firebase.json)

- `/api/**` → servicesApi (except task workers)
- `/api/tasks/export-worker` → exportWorker
- `/api/tasks/training-assignment` → trainingAssignmentWorker
- `/api/waitlist/**` → waitlistApi (landing hosting only)

## Backend-Only Writes (ADR-008)

All Firestore mutations go through Cloud Functions to ensure audit logging. The frontend is read-only for Firestore — all writes use REST API endpoints defined in `firebase.json`.

## Firestore Composite Indexes

**When writing new Firestore queries in `packages/data/src/repositories/`**, you MUST add a composite index to `firebase/firestore.indexes.json` if the query uses:

- `where()` + `orderBy()` on a different field
- Multiple `where()` clauses where any uses `in`, `array-contains`, `!=`, or range operators (`<`, `>`, `<=`, `>=`)

**The Firestore emulator does NOT enforce index requirements.** Missing indexes only fail in production with a 500 error. Always add the index in the same PR as the query.

Additionally, add a `hasIndex(...)` assertion in `packages/data/src/repositories/firestore-indexes.test.ts` to prevent index regressions. See [Testing Best Practices](../work-instructions/WI-005-test-best-practices.md) for the pattern.

See [HLD-003 — Data & Integration Flows](../architecture/HLD-003-data-integration-flows.md) for the full index strategy.

## Document Sync Architecture

Document sync uses Google Cloud Tasks for background processing:

1. **Trigger**: User clicks "Sync" button or GitHub webhook fires
2. **API** (`POST /api/v2/repositories/:id/sync`):
   - Creates `syncEvent` in Firestore (status: pending)
   - Queues Cloud Task to `document-sync` queue
   - Returns `syncEventId` to client
3. **Worker** (`syncWorker` Cloud Function):
   - Receives task from queue
   - Updates syncEvent (status: in_progress)
   - Fetches markdown files from GitHub
   - Parses documents and extracts frontmatter
   - Updates Firestore documents collection
   - Updates syncEvent (status: completed)
4. **Client**: Polls syncEvent for completion

**Key Files:**

- `apps/services/src/modules/repositories/RepositoriesService.ts` — Sync trigger
- `apps/functions/src/syncWorker.ts` — Cloud Tasks handler
- `apps/functions/src/shared/sync-engine.ts` — Core sync logic
- `apps/functions/src/shared/document-parser.ts` — Markdown parsing

**Local Development:**
Cloud Tasks not available in emulators. For local testing, use inline processing or mock the queue.
