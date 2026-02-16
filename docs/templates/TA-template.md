---
id: TA-{NNN}
title: '{Task analysis title}'
status: draft
links:
  - type: derives-from
    target: '{UN-NNN or PR-NNN}'
---

# {Title}

## Overview

{Brief description of the user task being analyzed. What is the user trying to accomplish and in what context?}

---

## Flow 1: {Primary Flow Name}

### Entry Point

{How the user begins this task — navigation path, trigger, or precondition.}

### Steps

| #   | User Action | System Response | Notes            |
| --- | ----------- | --------------- | ---------------- |
| 1   | {action}    | {response}      | {optional notes} |
| 2   | {action}    | {response}      |                  |

### Success Criteria

- {What indicates the task was completed successfully}

### Error Scenarios

| Scenario          | User Sees                   | Recovery Path    |
| ----------------- | --------------------------- | ---------------- |
| {error condition} | {error message or UI state} | {how to recover} |

---

## Flow 2: {Alternative or Secondary Flow}

{Repeat the flow structure above for each significant variation.}

---

## Usability Considerations

- {Accessibility concern}
- {Cognitive load consideration}
- {Frequency of use and expertise level}

## Related Documents

| ID          | Title                    | Relationship                  |
| ----------- | ------------------------ | ----------------------------- |
| US-UV-{NNN} | {Terminology validation} | Terminology used in this flow |
| UN-{NNN}    | {User need}              | Need this task addresses      |

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | YYYY-MM-DD | {name} | Initial draft |
