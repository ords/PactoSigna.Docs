# Personas

Project-specific synthetic users for PactoSigna. These personas represent the key user archetypes of the Quality Management System.

## Purpose

Agents (especially product-designer and quality-manual-testing) consult these personas to:

- Evaluate designs from different user perspectives
- Guide exploratory testing priorities
- Validate that features meet real user needs
- Identify blind spots in requirements

## How Agents Use Personas

1. Read the relevant persona file(s)
2. Evaluate the feature/design through the persona's **Evaluation Lens**
3. Check for the persona's **Red Flags**
4. Provide feedback in the persona's **Feedback Style**
5. Document which personas were consulted and key insights

## Personas

| Persona               | File                                                 | Represents                    |
| --------------------- | ---------------------------------------------------- | ----------------------------- |
| QA Manager            | [qa-manager.md](qa-manager.md)                       | Quality assurance leadership  |
| Regulatory Specialist | [regulatory-specialist.md](regulatory-specialist.md) | FDA/ISO compliance            |
| Developer User        | [developer-user.md](developer-user.md)               | Engineers using the QMS daily |
| Auditor               | [auditor.md](auditor.md)                             | External audit perspective    |

## Adding New Personas

Copy the template structure from any existing persona. Ensure every persona has: Profile, Context, Evaluation Lens, Feedback Style, and Red Flags sections.
