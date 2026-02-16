---
id: 'US-{subcategory}-{NNN}'
title: '{Use specification title}'
status: draft
links:
  - type: derives-from
    target: '{UN-NNN or PR-NNN}'
---

# {Title}

## 1. Purpose

{What this use specification covers. Subcategory determines the focus:}

- **US-UP**: User Profile / Persona
- **US-UV**: Use Validation / Terminology
- **US-UN**: User Needs research
- **US-DR**: Design Research

## 2. Subcategory Details

| Field        | Value                                                         |
| ------------ | ------------------------------------------------------------- |
| Subcategory  | {UP (Profile) / UV (Validation) / UN (Needs) / DR (Research)} |
| Feature Area | {Document Management / Release Management / etc.}             |

---

{Content structure depends on subcategory. Examples below.}

## For US-UP (User Profile / Persona)

### Profile

- **Name**: {persona name}
- **Role**: {job title and context}
- **Experience**: {relevant background}
- **Primary Concerns**: {key motivations}

### Context

{Day-to-day work context and pain points.}

### Evaluation Lens

{How this persona evaluates the product — what they look for.}

---

## For US-UV (Terminology Validation)

### Terminology Map

| UI Term | Definition   | Alternative Terms Considered | Decision Rationale         |
| ------- | ------------ | ---------------------------- | -------------------------- |
| {term}  | {definition} | {alternatives}               | {why this term was chosen} |

---

## For US-UN (User Needs Research)

### Research Method

{Interviews, surveys, observation, competitive analysis, etc.}

### Findings

{Key findings organized by theme.}

### Derived User Needs

| Need ID  | Need Statement | Priority                |
| -------- | -------------- | ----------------------- |
| UN-{NNN} | {statement}    | {Essential / Desirable} |

---

## For US-DR (Design Research)

### Research Question

{What question this research aims to answer.}

### Methodology

{How the research was conducted.}

### Key Findings

{Summarized findings with evidence.}

### Design Recommendations

{Actionable recommendations derived from the research.}

---

## Traceability

| Direction | Link Type    | Target ID | Title                 |
| --------- | ------------ | --------- | --------------------- |
| Up        | derives-from | UN-{NNN}  | {User need title}     |
| Down      | refines      | TA-{NNN}  | {Task analysis title} |

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | YYYY-MM-DD | {name} | Initial draft |
