# [Project Name]

[One sentence describing what this project does.]

## Stack

<!-- Add your stack and project-specific conventions here. Example:
- **Backend:** Go — Chi router
- **Frontend:** React + Vite + Tailwind CSS
- **Database:** PostgreSQL
-->

## Document Structure

- `documents/northstar.md` — product vision, target users, non-negotiables, capability map
- `documents/capabilities/` — one file per system capability; functional requirements, data model, acceptance criteria
- `documents/roadmaps/` — implementation roadmaps for each capability
- `documents/decisions/` — cross-cutting and architectural decisions that apply project-wide

Read the relevant capability doc before working on a feature. If there is no capability doc yet, check the northstar for alignment.

## Decision Tracking

Decisions are recorded in two places depending on scope:

**Per-package** (`<package>/DECISIONS.md`): Code-level choices — why a library was chosen, why a function is structured a specific way, constraints the next person touching this package must know. Create this file when the first meaningful decision is made for that package.

**Project-wide** (`documents/decisions/<topic>.md`): Cross-cutting or architectural choices — API conventions, module naming, tooling choices, patterns that apply across the whole codebase.

Write a decision when:
- A non-obvious choice was made between real alternatives
- A constraint exists that future code must not violate
- A conversation drove toward a specific approach that could easily be undone by the next agent

If a conversation produces a key decision, record it before the conversation ends — do not rely on a future step to capture it.

## Conventions

<!-- Add project-specific conventions here. Example:
- Module path: `myproject/internal/...`
- DB driver: `pgx/v5`
-->

## Skills

Workflow skills live in `.agents/skills/<name>/SKILL.md`. Each is invoked by name (e.g. `draft-northstar`, `draft-capability`, `imp`, `audit`) — see the root `README.md` for the full list and the order they run in.
