# Training Module - Flow Specification

Issue: PactoSigna/Sample.QMS.ISO13485#3

## Screen Inventory

| Screen                 | Route                           | Purpose                          | Access                  |
| ---------------------- | ------------------------------- | -------------------------------- | ----------------------- |
| My Training            | `/training`                     | Member's pending/completed tasks | All members             |
| Training Task Detail   | `/training/:taskId`             | Single task with document + quiz | Task assignee           |
| Training Dashboard     | `/admin/training`               | Team overview, reports           | Admin, Manager          |
| Training Matrix Export | `/admin/training/export`        | Audit-ready PDF/CSV              | Admin, Manager, Auditor |
| Batch Sign-off         | `/admin/training/batch-signoff` | Instructor-led bulk completion   | Admin, Manager          |
| Training Settings      | `/settings/training`            | Org-level training config        | Admin                   |

## Component Hierarchy

```
App
├── Layout
│   └── Sidebar
│       └── TrainingNavItem (badge with pending count)
│
├── MyTrainingPage
│   ├── TrainingFilterBar (status: pending|completed|all)
│   └── TrainingTaskList
│       └── TrainingTaskCard[]
│           ├── DocumentTitle
│           ├── TrainingType badge (quiz|acknowledge)
│           ├── DueDate (with overdue styling)
│           └── ActionButton (Start|Continue|View)
│
├── TrainingTaskDetailPage
│   ├── DocumentViewer (content from GitHub)
│   ├── TrainingActions
│   │   ├── TakeQuizButton (for quiz type)
│   │   └── AcknowledgeButton (for acknowledge type)
│   └── QuizModal (when quiz active)
│       ├── QuizProgress (question X of Y)
│       ├── QuizQuestion
│       │   ├── QuestionText
│       │   └── OptionList (randomized)
│       ├── QuizNavigation (prev|next)
│       └── QuizSubmit
│
├── TrainingDashboardPage (admin)
│   ├── TrainingStatsCards
│   │   ├── PendingCount
│   │   ├── OverdueCount
│   │   └── CompletedThisMonth
│   ├── TeamTrainingTable
│   │   ├── MemberRow[]
│   │   │   ├── MemberName
│   │   │   ├── Department
│   │   │   ├── PendingTasks
│   │   │   └── CompletionRate
│   │   └── TableFilters
│   └── TrainingMatrixExportButton
│
├── BatchSignoffPage (admin)
│   ├── TrainingTaskSelector (multi-select tasks)
│   ├── EmployeeSelector (multi-select employees)
│   ├── SignInSheetUploader
│   │   ├── FileDropzone (PDF only)
│   │   ├── UploadProgress
│   │   └── FilePreview
│   └── BatchSignoffActions
│       ├── VerifyButton (check hash)
│       └── SignoffButton (manager signature)
│
├── TrainingSettingsPage (admin)
│   ├── DefaultDueDaysInput (number, e.g., 14)
│   └── PassingScoreInput (percentage, e.g., 80%)
│
└── Dialogs
    ├── QuizResultDialog (pass/fail with score, unlimited retries)
    ├── AcknowledgeDialog (confirm + signature)
    ├── BatchSignoffDialog (confirm bulk completion)
    └── SignatureDialog (reused from signatures feature)
```

## State Machines

### TrainingTask Status

```
                    ┌─────────┐
                    │ pending │
                    └────┬────┘
                         │ user opens task
                         ▼
                  ┌─────────────┐
                  │ in_progress │
                  └──────┬──────┘
                         │
         ┌───────────────┼───────────────┐
         │               │               │
         ▼               ▼               ▼
    ┌─────────┐    ┌───────────┐   ┌─────────┐
    │completed│    │  overdue  │   │(retry)  │
    └─────────┘    └───────────┘   └────┬────┘
         ▲                              │
         └──────────────────────────────┘
                   pass quiz
```

### Quiz Session

```
┌──────────┐     ┌─────────────┐     ┌───────────────┐
│ not_started│──▶│ in_progress │──▶│ awaiting_submit│
└──────────┘     └──────┬──────┘     └───────┬───────┘
                        │                     │
                        │ navigate questions  │ submit
                        ▼                     ▼
                  ┌───────────┐         ┌──────────┐
                  │(same state│         │ submitted│
                  │ question  │         └────┬─────┘
                  │ changes)  │              │
                  └───────────┘              ▼
                                    ┌─────────┐  ┌──────┐
                                    │ passed  │  │failed│
                                    └─────────┘  └──────┘
```

## Data Flow

### Quiz Completion Flow

```
                                   ┌─────────────────────┐
                                   │    Frontend         │
                                   └──────────┬──────────┘
                                              │
                  1. GET /training/tasks/my   │
                  ◀───────────────────────────┤
                                              │
                  2. GET /documents/:id/content
                  ◀───────────────────────────┤ (fetches from GitHub)
                                              │
                  3. GET /documents/:id/quiz  │
                  ◀───────────────────────────┤ (returns shuffled quiz)
                                              │
                  4. POST /training/:taskId/quiz/submit
                  ◀───────────────────────────┤
                     { answers: [...] }       │
                                              │
                  5. Response: { passed, score, signatureRequired }
                  ────────────────────────────▶
                                              │
                  6. POST /signatures         │ (if passed)
                  ◀───────────────────────────┤
                     { type: 'trainee', ... } │
                                              │
                  7. PATCH /training/:taskId  │
                  ◀───────────────────────────┤
                     { status: 'completed' }  │
                                              ▼
```

## Quiz Randomization Spec

**Requirement:** Prevent answer-sharing by randomizing questions and options.

**Implementation:**

1. Server generates a random seed when quiz session starts
2. Seed is stored with the quiz session (in memory or Firestore)
3. Same seed used for shuffling questions and options
4. On submission, server uses same seed to map answers back to correct positions
5. Seed is single-use; new quiz attempt gets new seed

```typescript
interface QuizSession {
  taskId: string;
  userId: string;
  seed: number;
  startedAt: Timestamp;
  questionOrder: number[]; // shuffled question indices
  optionOrders: number[][]; // shuffled option indices per question
}
```

## Instructor-Led Training Flow

### Batch Sign-off Data Flow

```
                                   ┌─────────────────────┐
                                   │    Frontend         │
                                   └──────────┬──────────┘
                                              │
                  1. GET /training/tasks?type=instructor_led
                  ◀───────────────────────────┤
                                              │
                  2. Manager selects tasks + employees
                                              │
                  3. POST /storage/upload     │
                  ◀───────────────────────────┤
                     { file: PDF }            │
                                              │
                  4. Response: { url, hash }  │
                  ────────────────────────────▶ (Firebase Storage signed URL)
                                              │
                  5. POST /training/batch-signoff
                  ◀───────────────────────────┤
                     { taskIds, memberIds,    │
                       evidenceUrl, hash }    │
                                              │
                  6. Manager Part 11 signature│
                  ────────────────────────────▶
                                              │
                  7. Tasks marked completed   │
                                              ▼
```

### Evidence Integrity Verification

```typescript
// On upload: compute SHA256 hash of file
const hash = await computeSHA256(file);

// Store in training record
{
  evidenceUrl: 'gs://bucket/path/signin-sheet.pdf',
  evidenceHash: 'a1b2c3d4...'  // SHA256
}

// On audit: re-fetch file and verify hash matches
const currentHash = await computeSHA256(await fetch(signedUrl));
const isValid = currentHash === record.evidenceHash;
```

## Due Date Configuration

### Organization Settings

```typescript
// In organization document
{
  training: {
    defaultDueDays: 14,         // Default for all training
    passingScorePercent: 80    // Quiz passing threshold
  }
}
```

### Document Frontmatter Override

```yaml
---
id: SOP-001
title: Critical Safety Procedure
training:
  required_roles: ['QA', 'Production']
  training_mode: quiz
  due_days: 7 # Override: urgent training
---
```

### Due Date Calculation

```typescript
// When creating training task:
const dueDays = document.training?.due_days ?? organization.training?.defaultDueDays ?? 14;

const dueDate = addDays(createdAt, dueDays);
```

## API Endpoints Required

| Method | Endpoint                                      | Purpose                                    |
| ------ | --------------------------------------------- | ------------------------------------------ |
| GET    | `/api/v2/training/my-tasks`                   | List member's training tasks               |
| GET    | `/api/v2/training/:taskId`                    | Get single task detail                     |
| PATCH  | `/api/v2/training/:taskId`                    | Update task (start, complete)              |
| POST   | `/api/v2/training/:taskId/quiz/start`         | Start quiz session (returns shuffled quiz) |
| POST   | `/api/v2/training/:taskId/quiz/submit`        | Submit quiz answers (unlimited retries)    |
| POST   | `/api/v2/training/:taskId/acknowledge`        | Complete acknowledge-type task             |
| POST   | `/api/v2/training/batch-signoff`              | Instructor-led bulk completion             |
| POST   | `/api/v2/storage/upload`                      | Upload sign-in sheet to Firebase Storage   |
| GET    | `/api/v2/storage/:id/verify`                  | Verify file hash for integrity             |
| GET    | `/api/v2/training/dashboard`                  | Admin: team training stats                 |
| GET    | `/api/v2/training/export`                     | Export training matrix                     |
| GET    | `/api/v2/organizations/:id/training-settings` | Get org training config                    |
| PATCH  | `/api/v2/organizations/:id/training-settings` | Update org training config                 |

## Quiz Format (JSON)

Quizzes are stored as JSON files alongside their SOP in `docs/sop/`:

```
docs/sop/
├── SOP-001.md
├── SOP-001-quiz.json    ← Quiz for SOP-001
├── SOP-002.md
└── SOP-002-quiz.json
```

**Naming convention:** `{document-id}-quiz.json` (e.g., `SOP-001-quiz.json`)

**Derived fields:**

- Quiz ID → from filename
- Version → from commit SHA at sync
- Document link → filename match

### JSON Schema

```json
{
  "$schema": "https://pactosigna.com/schemas/quiz.json",
  "passingScore": 80,
  "questions": [
    {
      "type": "single-choice",
      "text": "What is the first step when handling a customer complaint?",
      "options": [
        { "text": "Ignore it" },
        {
          "text": "Document the complaint in the system",
          "correct": true,
          "explanation": "Per Section 4.2, all complaints must be logged within 24 hours."
        },
        { "text": "Escalate to legal immediately" },
        { "text": "Delete the email" }
      ]
    },
    {
      "type": "multi-choice",
      "text": "Which are required for Part 11 compliance? (Select all that apply)",
      "options": [
        { "text": "Audit trail", "correct": true },
        {
          "text": "Electronic signatures",
          "correct": true,
          "explanation": "21 CFR Part 11.50 requires electronic signatures."
        },
        { "text": "Paper backup" },
        { "text": "Access controls", "correct": true }
      ]
    },
    {
      "type": "true-false",
      "text": "Equipment calibration records must be retained for 5 years.",
      "correct": true,
      "explanation": "Per ISO 13485:2016 Section 7.6, calibration records are quality records."
    }
  ]
}
```

### Question Types

| Type            | Description              | Correct Field                       |
| --------------- | ------------------------ | ----------------------------------- |
| `single-choice` | One correct answer       | `correct: true` on one option       |
| `multi-choice`  | Multiple correct answers | `correct: true` on multiple options |
| `true-false`    | True or false statement  | `correct` at question level         |

### Field Reference

| Field                   | Required | Default     | Description                                   |
| ----------------------- | -------- | ----------- | --------------------------------------------- |
| `passingScore`          | No       | Org default | Percentage to pass (0-100)                    |
| `questions`             | Yes      | -           | Array of question objects                     |
| `questions[].type`      | Yes      | -           | `single-choice`, `multi-choice`, `true-false` |
| `questions[].text`      | Yes      | -           | Question text                                 |
| `questions[].options`   | Yes\*    | -           | Array of options (\*not for true-false)       |
| `questions[].correct`   | Yes\*    | -           | Boolean (\*only for true-false)               |
| `options[].text`        | Yes      | -           | Option text                                   |
| `options[].correct`     | No       | `false`     | Whether this option is correct                |
| `options[].explanation` | No       | -           | Shown after submission for correct answers    |

### Scoring

- All questions have equal weight
- Score = (correct answers / total questions) × 100
- Pass if score ≥ passingScore (org default or quiz override)

## Error States

| State                  | UI Treatment                                                                 |
| ---------------------- | ---------------------------------------------------------------------------- |
| No pending training    | Empty state: "You're all caught up!"                                         |
| Quiz fetch failed      | Error banner with retry button                                               |
| Quiz submit timeout    | "Connection lost. Your answers are saved. Retry?"                            |
| Quiz failed            | "Score: X%. You need Y% to pass. Re-read and try again." (unlimited retries) |
| Already completed      | Show completed state, link to view record                                    |
| Document not found     | "Document no longer exists. Contact admin."                                  |
| Task overdue           | Red badge on card, still completable                                         |
| Evidence upload failed | "Upload failed. Please try again." with retry                                |
| Evidence hash mismatch | "Warning: File integrity check failed. Contact admin."                       |

## Configuration Defaults

| Setting              | Default   | Configurable At                    |
| -------------------- | --------- | ---------------------------------- |
| Training due days    | 14        | Organization, Document frontmatter |
| Passing score        | 80%       | Organization                       |
| Quiz retry limit     | Unlimited | Not configurable (by design)       |
| Overdue grace period | 0 days    | Not configurable                   |
