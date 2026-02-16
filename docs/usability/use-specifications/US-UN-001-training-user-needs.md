# Training Module - Jobs-to-be-Done Analysis

Issue: PactoSigna/Sample.QMS.ISO13485#3

## User Roles

### Staff Member (Trainee)

**Situation:** I've just been notified that a new SOP has been published.

**Motivation:** Stay compliant with company procedures to do my job correctly and avoid audit findings.

**Expected Outcome:** Complete training quickly without disrupting my work, with clear proof I've been trained.

**Job Statement:** When a new or updated procedure is published, help me understand what I need to learn and verify my understanding efficiently, so I can stay compliant without excessive time away from my primary work.

### Manager

**Situation:** My team handles regulated processes and I'm accountable for their training.

**Motivation:** Ensure my team is trained before they perform regulated work, avoid audit findings.

**Expected Outcome:** Clear visibility into who needs training, who has completed it, and be able to take action on overdue training.

**Job Statement:** When procedures change, help me track my team's training progress and take action on overdue items, so I can demonstrate to auditors that my team is qualified.

### QA/Training Administrator

**Situation:** A release has been published with SOP changes.

**Motivation:** Efficiently assign training to the right people without manual overhead.

**Expected Outcome:** Training automatically assigned based on role/department, with minimal admin work.

**Job Statement:** When documents are updated, automatically assign training to affected staff based on their role, so I don't have to manually track who needs what training.

### Auditor

**Situation:** Regulatory audit or internal review of training effectiveness.

**Motivation:** Verify that staff were trained on the correct version of procedures before performing work.

**Expected Outcome:** Audit-ready training matrix showing who was trained on what, when, and with proof.

**Job Statement:** When reviewing compliance, help me generate reports showing training records tied to specific document versions, so I can demonstrate training effectiveness to regulators.

## Success Metrics

| Role         | Metric                           | Target                                             |
| ------------ | -------------------------------- | -------------------------------------------------- |
| Staff Member | Time to complete training task   | < 15 minutes for quiz, < 5 minutes for acknowledge |
| Staff Member | Training completion rate         | > 95% on-time                                      |
| Manager      | Overdue training visibility      | 100% (no hidden gaps)                              |
| QA Admin     | Manual training assignments      | 0 (fully automated)                                |
| Auditor      | Time to generate training matrix | < 30 seconds                                       |

## Pain Points (Current State)

1. **Manual tracking** - Training tracked in spreadsheets, easily gets outdated
2. **Version mismatch** - Hard to prove training was on the correct document version
3. **No automation** - Admin manually identifies who needs training after each release
4. **Scattered evidence** - Sign-in sheets in filing cabinets, digital records fragmented
5. **Quiz integrity** - Paper quizzes can be shared; answers circulate

## Design Principles

1. **Open Book Philosophy** - Training should test understanding, not memorization. Users can reference the SOP during the quiz.
2. **Randomization for Integrity** - Shuffle questions and options to prevent answer-sharing.
3. **Version Binding** - Every training record tied to exact commit SHA.
4. **Minimal Friction** - Training completion should be fast; complexity is in the system, not the UX.
5. **Audit-Ready** - Every action logged, every record immutable.
