---
name: task-hunt
description: Use when exploring a section of the codebase, uncovering unformed feature ideas, noticing improvements or tech debt, or hunting requirements to produce actionable task postulates before planning or coding.
---

# Task Hunt (`/task-hunt`)

Turn rough observations or codebase explorations into a clean list of tasks saved in a daily ledger.

Investigate the code silently first, then ask the user about scope and design decisions, then write the tasks to `.scratch/tasks/`.

---

## Step 1 — Read the Code First

When the user triggers the hunt, investigate before speaking.

1. **Find the target**:
   - If the user mentioned a topic or file, inspect it directly.
   - If invoked with no arguments, check the user's active editor file first, then uncommitted git diff (silently skip the diff check in non-git directories), then fall back to asking.
2. **Inspect the area**: Use `view_file` and `grep_search` to trace data flow, schemas, components, and contracts around the target.
3. **Check surrounding code**: Look at adjacent files for friction, hidden coupling, stale logic, unhandled edge cases, or easy cleanup opportunities.
4. **Form a rough list** of candidate tasks — the user's primary intent plus anything you noticed nearby.

> **Rule**: Never ask the user a question whose answer can be found by reading the codebase. Facts are your job; decisions are the user's.

**Completion Criterion**: You have a concrete list of file paths, verified constraints, and candidate tasks ready to discuss with the user.

---

## Step 2 — Ask About Scope and Decisions

Interview the user to resolve remaining uncertainty.

1. **Focus on code and requirements only**:
   - Scope boundaries (what is in-scope vs explicitly out-of-scope).
   - Behavioral trade-offs, fallback logic, and edge cases.
   - Whether side-tasks you discovered should be included or dropped.
2. **Guardrail**: Only ask questions about code logic, domain requirements, and feature scope. Never question or prompt about date formats, file locations, or tool conventions.
3. **Use `ask_question`** to provide structured multiple-choice options with a recommended default, leaving write-in space for custom input.
4. **Ask in rounds**: As answers settle open questions, ask dependent follow-ups until ambiguity is resolved or the user says they're ready.
5. **Escape hatch**: If the user gives terse responses or says something like "just record what you found," take your own recommended defaults for unresolved questions and proceed to writing. Don't loop.

**Completion Criterion**: The user has confirmed the scope and key decisions, or has signaled to proceed with your best judgment on open questions.

---

## Step 3 — Prepare the Ledger File

Tasks are recorded in a daily ledger inside `.scratch/tasks/`.

1. **Git check**: If running in a git repo and `.scratch/` is not ignored, append `.scratch/` to `.gitignore`.
2. **Date format is `DD-MM-YYYY`** (e.g., `11-09-2026.md`). Never question or suggest changing this format.
3. **Check if today's file exists**:
   - **If it exists**: Use `ask_question` to ask where to save:
     - `(Recommended) Append to today's ledger (.scratch/tasks/DD-MM-YYYY.md)`
     - `Save to another date (enter DD-MM-YYYY in write-in)`
   - **If it does not exist**: Create `.scratch/tasks/DD-MM-YYYY.md` directly.

**Completion Criterion**: Target file path is confirmed and `.scratch/` is git-ignored.

---

## Step 4 — Write the Tasks

Append or write the tasks to the target file.

> **CRITICAL**: Write like an engineer jotting notes, not like a project manager filling out a template. No rigid headings like `**Motivation**`, `**What Needs to Change**`, `**Decisions Made**`, `**Acceptance Criteria**`. Simple tasks get 1–2 sentences. Complex tasks get short sub-bullets only when they genuinely need it.

### Format

If creating a new file, start with:
```markdown
# Tasks & Discoveries — DD-MM-YYYY
```

For each session, append a topic section. Get the timestamp from the `ADDITIONAL_METADATA` local time field (or a system clock command as fallback):

```markdown
## Topic: <Descriptive Area Name>
_Captured at <HH:MM> — from: [SourceFile.jsx](file:///path/to/SourceFile.jsx#L10-L40)_

- [ ] **<Task title>**: <What needs to happen and why>. ([file.jsx:L10-L25](file:///path/to/file.jsx#L10-L25))
  - Sub-note only when there's a genuine nuance, tradeoff, or gotcha worth recording.

- [ ] **<Another task>**: One-liner when nothing else needs saying. ([anotherFile.js:L50](file:///path/to/anotherFile.js#L50))
```

Before writing, scan existing tasks in the target ledger for overlapping file paths or similar titles. If near-duplicates exist, flag them to the user during Step 2 instead of silently creating redundant entries.

### Finishing
1. Ensure `.scratch/tasks/` exists and write/append the content.
2. Link the saved file in chat: `[DD-MM-YYYY.md](file:///path/to/.scratch/tasks/DD-MM-YYYY.md)`.
3. If the user wants to act on a task immediately, suggest `/writing-plans` or `/implement`. If they're just cataloging, end cleanly.

**Completion Criterion**: File written, verified with `view_file`, linked in the response, and next steps offered if appropriate.
