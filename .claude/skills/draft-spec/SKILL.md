---
name: draft-spec
description: Draft a function-scoped feature spec through guided conversation. Use when the developer says '/draft-spec' or wants to clarify, scope, and document a feature before implementation. Writes to scratchpad/feature-spec.md.
allowed-tools: Read, Write, Glob, Grep, Bash(git diff --name-only)
---

## Purpose
A concise, repeatable workflow to clarify feature requests, restate understanding, map data flow, list impacted files and functions, edge cases, and produce function-scoped action items — without implementing any code. Always writes to `scratchpad/feature-spec.md` (single living file, overwritten each run).

One spec covers exactly one roadmap step. This is a hard rule — the spec header records which roadmap step it corresponds to, and `/imp` uses that to update progress tracking when complete.

## Workflow

1. **Input & Initial Read** — Ingest the feature request. Read relevant files to understand context.

2. **Identify Capability and Roadmap Step** — List all capability files from `documents/capabilities/` with numbers. Ask: "Is this request related to an existing capability? Type the number, or 'no' if not related." **Wait for response before proceeding.**

   If the user selects a capability: read that capability doc and its matching roadmap from `documents/roadmaps/` in full. Then scan `documents/decisions/` — read any file whose topic is relevant to the feature (e.g. touches the same package, API layer, or data source). For each package the selected roadmap step targets, also read `<package>/DECISIONS.md` if it exists. Briefly state what you loaded ("Read: data-ingestion.md, internal/ingestion/limitless/DECISIONS.md") before continuing.

   Show the roadmap steps with completion status (✓ for `[x] complete`, open for `[ ] complete`). Ask: "Which roadmap step are you speccing? Type the step number." **Wait for response before proceeding.**

   Once a step is selected, actively compare the loaded decisions against the selected roadmap step. Look for: constraints that contradict the step's stated approach or architectural rules that change the target files. If any conflicts exist, surface them now — before the scope check and before any clarifying questions — and resolve them with the developer before continuing.

   Also scan the incomplete steps ahead in the roadmap. Ask: would any design choice in this spec foreclose or force rework on a future step? If yes, surface that before asking any clarifying questions so the spec accounts for it from the start.

   If no capability is selected, still scan `documents/decisions/` for relevant files and load any that apply. Skip roadmap step selection — the spec header will have no Roadmap Step line.

3. **Scope Check** — Before asking any clarifying questions, assess whether the selected roadmap step is appropriately sized for a single spec. A step is too large if it touches more than two files, implies more than ~8 functions, or its description covers separable phases of work that could ship independently.

   If the step is too large:
   - Propose a concrete split: name each sub-step and its target files
   - Ask the developer to confirm or adjust the breakdown
   - **Wait for confirmation before proceeding**
   - Rewrite the roadmap file in-place: replace the original step with the confirmed sub-steps, using letter suffixes (e.g. Step 4 → Step 4a and Step 4b). Preserve all other steps and their completion status unchanged
   - State which sub-step this spec will cover, then continue

   If the step is appropriately scoped, proceed directly to clarifying questions.

4. **Clarify Assumptions First** — This is the most important step. The spec is only as solid as the decisions made here. Be exhaustive: surface every assumption that could cause a wrong implementation or require rework. Do not proceed until every question is resolved.

   Ask questions in conversational bursts of 2–3 at a time, grouped by theme. **Wait for answers after each burst before asking the next.** If an answer raises a follow-up, ask it in the next burst — do not defer it to the end.

   Work through these areas in order, skipping any that provably do not apply:

   - **Requirements** — scope, boundaries, what this explicitly does not do
   - **Data contracts** — input/output shapes, required vs. optional vs. nullable fields
   - **Error handling** — upstream failures, partial failures, what the caller sees
   - **State and side effects** — what is written or mutated, idempotency
   - **Dependencies** — what this calls, whether those interfaces are stable
   - **Concurrency** — parallel execution, shared state
   - **Scope boundaries** — adjacent concerns that are explicitly out of scope
   - **Existing patterns** — codebase precedents, decisions docs that constrain the approach

   Keep going until every area is covered and nothing is unresolved. Only then move to step 5.

5. **Restate Understanding** — Briefly echo the confirmed scope.

6. **Analyze Data Flow** — Map end-to-end data paths (sources, transformations, calls, responses, side effects). Keep it concise — bullet points showing the request/response journey through the system.

7. **Create Action Items** — One action item per function. Each item must be independently reviewable — the developer should be able to form an opinion on it in under a minute without needing to hold another item in their head. For each function, mark **[Add]**, **[Change]**, or **[Remove]** and describe intent in a single sentence. If you can't describe a function's purpose in one sentence, it should be two functions. Never group multiple functions into one item. Include new functions if needed.

8. **Edge Cases** — List notable edge cases, failure modes, and validation needs (nil/empty, timeouts, permissions, config).

9. **Write Spec** — Create or overwrite `scratchpad/feature-spec.md` using the template below. Keep wording scannable. **Only write after all questions are answered.**

10. **Upstream coherence check** — After writing the spec, review the assumptions resolved in step 4 and the final spec. Ask: does anything decided here contradict or update what the capability doc or northstar claims?

    - **Capability doc** (occasional): If a scoping decision, data contract, or stated behavior in the spec differs from the capability doc, flag it — don't silently align the spec to incorrect docs. Present the conflict and ask the developer whether to update the capability doc or adjust the spec.
    - **Northstar** (rare): If a decision reveals that a guiding principle, system component description, or product boundary in `documents/northstar.md` is now wrong or outdated, flag it explicitly. Do not silently patch it — present the conflict and ask the developer how to resolve it before closing.

    If nothing conflicts, state that clearly. Do not manufacture amendments.

## Output Location
Always write/update: `scratchpad/feature-spec.md` (single living file per run).

## `feature-spec.md` Template

```markdown
# [Feature Name]

**Roadmap Step:** [capability-name] / Step N — [Step title from roadmap]

---

## Quick Start (TL;DR)

1. **[Action 1]** → `path/to/file` - Brief description
2. **[Action 2]** → `path/to/file:line` - Brief description
3. **[Action 3]** → `path/to/file` - Brief description
4. **Test**: `[test command]`

---

## Feature Summary

[2-3 sentences describing the goal, scope, and purpose]

---

## Action Items (Function-Scoped)

#### `path/to/file` **[Add/Change/Remove]**

- [ ] **1. `FunctionName`** (line XX)
  - **What**: Brief description of the change
  - **Why**: Reason for the change
  - **Input**: Parameters or data received
  - **Output**: Return type or data produced

- [ ] **2. `AnotherFunction`** (line YY)
  - **What**: Brief description
  - **Why**: Reason
  - **Input**: Parameters
  - **Output**: Return type

#### `path/to/another/file` **[Change]**

- [ ] **3. `MethodName`** (line ZZ)
  - **What**: Specific change needed
  - **Why**: Impact or reason
  - **Input**: Parameters
  - **Output**: Return type

[Repeat for each file, continuing the function count...]

---

## Data Flow

**Request Path:**
1. Client → [Entry point] - [What happens]
2. [Component A] → [Component B] - [Data transformation]
3. [Component B] → [External System] - [Side effect]
4. [External System] → [Component B] - [Response]
5. [Component B] → Client - [Final output]

**Key Types:**
- `TypeName1`: [Purpose and key fields]
- `TypeName2`: [Purpose and key fields]

**Side Effects:**
- Database writes to [table]
- Cache updates in [layer]
- External API calls to [service]

---

## Edge Cases

- **[Scenario 1]**: [What could go wrong] → [How to handle]
- **[Scenario 2]**: [Validation concern] → [Expected behavior]
- **[Scenario 3]**: [Error condition] → [Fallback strategy]
- **[Scenario 4]**: [Performance concern] → [Mitigation]

---

## Open Questions

- **Q1**: [Unclear requirement or blocker]
- **Q2**: [Decision needed before implementation]
- **Q3**: [Dependency or configuration question]

---
```

## Notes
- Spec only — no implementation.
- Unit tests are out of scope unless specifically requested.
- Keep concise and scannable (bullets, short sentences).
- If requirements are incomplete, surface blockers in Open Questions and seek clarification before writing the spec.
- Action item granularity: one function per item, independently reviewable. A function that can't be described in one sentence of intent is a design problem — split the function, not just the spec item.
- One spec = one roadmap step. The `Roadmap Step:` header line is not optional — `/imp` uses it to flip the correct roadmap checkbox when the spec is complete.
