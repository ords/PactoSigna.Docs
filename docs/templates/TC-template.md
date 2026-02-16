---
id: '{TP-NNN | TC-NNN | TC-NNN}'
title: '{Test document title}'
status: draft
links:
  - type: verified-by
    target: '{SWR-NNN or PR-NNN}'
---

# {Title}

## 1. Purpose

{What this test document covers — test plan, test case, or test report. Reference the requirement(s) being verified.}

## 2. Test Type

| Field         | Value                                                  |
| ------------- | ------------------------------------------------------ |
| Document Type | {Test Plan (TP) / Test Case (TC) / Test Report (TEST)} |
| Test Level    | {Unit / Integration / System / Acceptance}             |
| Test Method   | {Automated / Manual / Inspection / Analysis}           |
| Environment   | {Local emulators / CI / Staging / Production}          |

## 3. Scope

{What features, requirements, or components are covered by this test.}

### In Scope

- {item}

### Out of Scope

- {item}

## 4. Test Cases

### TC-{NNN}-01: {Test case title}

| Field         | Value                 |
| ------------- | --------------------- |
| Requirement   | {SWR-NNN or PR-NNN}   |
| Preconditions | {setup needed}        |
| Priority      | {High / Medium / Low} |

**Steps:**

| #   | Action   | Expected Result    |
| --- | -------- | ------------------ |
| 1   | {action} | {expected outcome} |
| 2   | {action} | {expected outcome} |

**Result:** {Pass / Fail / Blocked / Not Run}

### TC-{NNN}-02: {Test case title}

{Repeat for additional test cases.}

## 5. Test Results Summary

| Metric      | Value |
| ----------- | ----- |
| Total Cases | {N}   |
| Passed      | {N}   |
| Failed      | {N}   |
| Blocked     | {N}   |
| Pass Rate   | {%}   |

## 6. Defects Found

| Defect ID    | Test Case     | Severity               | Status        | Resolution    |
| ------------ | ------------- | ---------------------- | ------------- | ------------- |
| {GH issue #} | TC-{NNN}-{NN} | {Critical/Major/Minor} | {Open/Closed} | {description} |

## 7. Traceability

| Direction | Link Type | Target ID | Title                        |
| --------- | --------- | --------- | ---------------------------- |
| Up        | verifies  | SRS-{NNN} | {Software requirement title} |
| Up        | verifies  | PRS-{NNN}  | {Product requirement title}  |

## 8. Sign-off

| Role     | Name   | Date       | Signature |
| -------- | ------ | ---------- | --------- |
| Tester   | {name} | YYYY-MM-DD | {pending} |
| Reviewer | {name} | YYYY-MM-DD | {pending} |

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | YYYY-MM-DD | {name} | Initial draft |
