# Harness

A Claude Code SDLC workflow system — skills and document conventions for building software with AI agents.

## What's in here

**Skills** (`.claude/skills/`) — slash commands that run structured workflows in Claude Code:

| Skill | Trigger | Purpose |
|---|---|---|
| `draft-northstar` | `/draft-northstar` | Define or refine the project vision document |
| `draft-capability` | `/draft-capability` | Document a capability and generate its implementation roadmap |
| `draft-foundation` | `/draft-foundation` | Drive the architectural design conversation before coding begins |
| `draft-spec` | `/draft-spec` | Produce a function-scoped feature spec from a roadmap step |
| `imp` | `/imp step N` | Implement a specific spec step with review and progress tracking |
| `audit` | `/audit` | Review recently changed code for correctness, conventions, and security |
| `utest` | `/utest` | Propose and write unit tests one at a time with developer approval |
| `pr-create` | `/pr-create` | Open a GitHub PR from the current branch with a structured description |

**Document structure** (`documents/`) — the conventions the skills read and write:

- `documents/northstar.md` — product vision and capability map
- `documents/capabilities/` — one file per capability; requirements and acceptance criteria
- `documents/roadmaps/` — step-by-step implementation plans per capability
- `documents/decisions/` — cross-cutting architectural decisions

## How to use

1. Copy `.claude/` and `documents/` into your project root.
2. Fill in `CLAUDE.md` with your stack and project-specific conventions.
3. Run `/draft-northstar` to create your vision document.
4. Run `/draft-capability` to document a capability and generate its roadmap.
5. Work through the roadmap with `/draft-foundation` → `/draft-spec` → `/imp`.

## Workflow

```
/draft-northstar        → documents/northstar.md
/draft-capability       → documents/capabilities/<name>.md
                          documents/roadmaps/<name>.md
/draft-foundation       → <package>/DECISIONS.md
/draft-spec step N      → scratchpad/feature-spec.md
/imp step N             → code + updated roadmap + DECISIONS.md
/audit                  → scratchpad/audit-report.md
/utest                  → scratchpad/test-plan.md + test files
/pr-create              → GitHub PR
```

## scratchpad/

Working files (`feature-spec.md`, `audit-report.md`, `test-plan.md`) are written here during active work and excluded from git. They are per-session artifacts, not permanent records.
