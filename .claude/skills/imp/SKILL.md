---
name: imp
description: Implements a specific step from scratchpad/feature-spec.md. Use when the developer says '/imp step N' or wants to execute a planned implementation step. Reads spec and decisions context, implements with intent described per change, reviews the result, confirms build passes, and records decisions.
allowed-tools: Read, Edit, Write, Glob, Bash(go build ./...), Bash(go vet ./...), Bash(npm run build), Bash(npm run lint), Bash(cargo build), Bash(cargo clippy), Bash(python -m py_compile), Bash(git diff --name-only)
---

You are an execution agent. Implement exactly what the current plan step describes. Do not touch files, functions, or behaviour covered by other steps — if a change would require it, stop and report rather than proceeding.

Do not use TaskCreate, TaskUpdate, TaskGet, or TaskList tools. This skill has its own workflow — do not add external task tracking on top of it.

## 1. Load context

Read these files before touching any code:
- `scratchpad/feature-spec.md` — note the `Roadmap Step:` line at the top (used in step 7); then find the action item matching `$ARGUMENTS` (e.g. "step 1" means the bullet `- [ ] **1. ...`); identify which `####` file-grouping header it falls under to know which file to touch; read the item's description in full
- For each package the step touches, read `<package>/DECISIONS.md` if it exists — these contain code-level constraints that must not be violated
- `documents/decisions/` — scan any files whose topic overlaps with the step; these contain cross-cutting architectural decisions
- The roadmap file named in the `Roadmap Step:` header (e.g. `documents/roadmaps/data-ingestion.md`) — read it in full. Note which steps are already complete and which remain. Before coding, scan the incomplete steps ahead and ask: would any design choice in this step force rework or block a future step? If yes, surface that to the developer before proceeding.

If the requested step does not exist in `scratchpad/feature-spec.md`, stop and report back. Do not guess.

## 2. Implement

**Scope:** Implement action item N. You may group item N with immediately adjacent items only if each is a trivially small change (a few lines) — the combined diff must remain reviewable at a glance. When in doubt, implement one item and stop.

Work through the step's bullet points in order, one at a time. For each bullet point, state your intent in one sentence before making any changes. Make changes in the smallest meaningful increments — a single function, a single behaviour, a single wiring — before moving to the next bullet.

**Hard boundary:** When the last bullet point of the requested step is done, stop. Do not implement any bullet points or files from the next numbered step, even if they are in the same package or the next step looks trivial.

Stay within the files the step specifies. If a change requires touching something outside scope, stop and report.

**Inline comments:** When writing or modifying code, add a comment at the decision point if:
- A non-obvious design choice was made (explain **why**)
- The purpose of a block or function isn't immediately clear from its name (explain **what**)
- A constraint or invariant the reader needs to know is being maintained

Comments answer "Why" and "What" — not "How" (the code shows that). Keep them concise. Do not add comments to self-evident code.

## 4. Review pass

After implementation is complete, do one full review pass. Check for:
- **Correctness** — does it match what the plan step describes?
- **Edge cases** — unhandled nil values, empty inputs, boundary conditions
- **Error handling** — surfaces failures to the user rather than silently failing
- **Security** — no injection vectors, no hardcoded secrets, no unsafe operations
- **Design patterns** — is the approach idiomatic for this language and framework? flag anything a senior engineer would question
- **Performance** — identify any obvious bottlenecks, unnecessary allocations, latency concerns, or areas where throughput could be improved with minimal added complexity
- **Idiomatic style** — consistent with the rest of the codebase

List each finding with the **file path and line number** it refers to. For each one, state whether you recommend fixing it now or deferring.

Present the list to the developer and ask: **"Fix any of these now, defer all to audit, or a mix?"**

Wait for their response, then act on it.

## 5. Build check

Detect the stack by checking for these files in the project root:

| File | Stack | Commands |
|---|---|---|
| `go.mod` | Go | `go build ./...` then `go vet ./...` |
| `package.json` | Node / TS | `npm run build` then `npm run lint` |
| `Cargo.toml` | Rust | `cargo build` then `cargo clippy` |
| `requirements.txt` / `pyproject.toml` | Python | `python -m py_compile <changed files>` |

Run the appropriate commands for the detected stack. If either fails, fix the error before continuing. Do not move on with a broken build.

## 6. Human review prompt

State that implementation is complete. Summarize what was built in 2–3 sentences.

Then provide a numbered test checklist the developer can walk through to verify the feature manually. Each item should be a concrete, observable action and expected result — specific enough that someone unfamiliar with the internals can follow it. Example format:

```
Test checklist:
1. <action> → expected result
2. <action> → expected result
```

Prompt: **"Ready for your review. Walk through the checklist above, then let me know when you're done and I'll record any decisions."**

Wait for the developer to confirm.

## 7. Update progress tracking

Mark this step complete across the planning documents.

**Feature spec** (`scratchpad/feature-spec.md`): Mark all `- [ ]` action item checkboxes that were implemented in this step as `- [x]`.

**Roadmap and capability** (only if the full feature-spec is now complete): After marking the spec checkboxes above, re-read `scratchpad/feature-spec.md`. If every action item checkbox is now `[x]` — none remain as `[ ]` — the full feature is implemented. In that case:

- **Roadmap** (`documents/roadmaps/`): Use the `Roadmap Step:` line at the top of the spec to identify the exact capability and step (e.g. `data-ingestion / Step 3`). Open that roadmap file and flip the matching step's `- [ ] complete` to `- [x] complete`. Do not guess or match by title — use the header value directly.
- **Capability** (`documents/capabilities/`): Read the capability doc that corresponds to this work. For any `- [ ]` acceptance criterion the feature satisfies, mark it `- [x]`. List what was marked for the developer.

If any `[ ]` items remain in the spec, skip both roadmap and capability updates entirely.

---

## 8. Record decisions

For each meaningful decision made during this step, record it in the right place:

**Code-level decisions** (library choice, structural constraint, "don't change this" note): write to `<package>/DECISIONS.md` for the package where the decision manifested. Create the file if it doesn't exist. Use this format:

```
## <short title>

**Why:** <reason the choice was made>
**Constraint:** <what future code must not violate, or omit if none>
```

**Cross-cutting or architectural decisions** (module conventions, tooling, patterns that apply project-wide): write to `documents/decisions/<topic>.md`. Create the file if it doesn't exist, or append to an existing file if the topic already has one.

If no meaningful decisions were made, skip this step — do not write a "no decisions" entry.

**Upstream coherence check** — After recording decisions, review what was just written against the remaining incomplete roadmap steps and the capability doc. Ask: does any recorded decision contradict or materially change how a future roadmap step should be approached? Does it invalidate an assumption in the capability doc?

This check has three tiers:

- **Roadmap** (common): If a decision changes the approach, target files, or scope of a remaining step, propose the amendment inline — state which step, what should change, and why. Ask the developer to confirm before editing the roadmap file.
- **Capability** (rare): If a decision reveals that a functional requirement, acceptance criterion, or stated constraint in the capability doc is now wrong, flag it explicitly. Do not silently patch it — present the conflict and ask the developer how to resolve it before making any edits.
- **Northstar** (very rare): If a decision reveals that a guiding principle, system component description, or product boundary in `documents/northstar.md` is now wrong or outdated, flag it explicitly. Do not silently patch it — present the conflict and ask the developer how to resolve it before making any edits.

If no recorded decision has upstream implications, state that clearly and stop. Do not manufacture amendments.

Then stop. The step is complete.
