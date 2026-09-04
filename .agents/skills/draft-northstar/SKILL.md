---
name: draft-northstar
description: Create or refine the project Northstar vision document through guided conversation. Use when the developer says '/draft-northstar' or wants to define or update the high-level project vision.
---

# Northstar Project Vision Workflow

This workflow helps define and document the high-level vision for the project through an interactive conversation.

## Steps

1. **Initiate the conversation** — Start by asking about the project. If no project name is provided, ask for it. If a `documents/northstar.md` already exists, read it first and ask whether this is a refinement pass or a full rewrite.

2. **Collect supporting context** — Ask if there are any supporting documents or notes to reference. If provided as file paths, read them. If provided as pasted text, work from that directly.

3. **Gather project overview** — Ask about:
   - What does this application do? What problem does it solve?
   - What are the core capabilities or main functions?
   - What makes this project unique or valuable?

4. **Identify stakeholders** — Discuss:
   - Who are the primary users?
   - Are there any external partners, integrations, or data dependencies?

5. **Define purpose and goals** — Explore:
   - What is the intended outcome for the user?
   - What does success look like?
   - What is the long-term vision?

6. **Understand context** — Cover:
   - What is the current state (greenfield, active development, enhancement)?
   - Are there technical constraints or requirements?
   - What is the competitive landscape or market context?

7. **Capture scope boundaries** — Clarify:
   - What is explicitly in scope?
   - What is explicitly out of scope?
   - What are the key dependencies or prerequisites?

8. **Document strategic priorities** — Identify:
   - What are the top 3-5 strategic priorities?
   - What trade-offs have been made (speed vs quality, features vs simplicity)?
   - What are the non-negotiables?

9. **Create the Northstar document** — Generate a comprehensive Northstar document at `documents/northstar.md` with all gathered information structured clearly.

10. **Review and refine** — Present the document and ask if anything needs to be added, clarified, or adjusted. Iterate until satisfied.

## Output

The workflow produces a `documents/northstar.md` document containing:
- Project name and tagline
- Executive summary
- Problem statement and solution
- Target users and stakeholders
- Core capabilities
- Strategic goals and success metrics
- Scope and boundaries
- Technical context
- Strategic priorities and trade-offs

This document serves as the foundation for all capability planning and development work.
