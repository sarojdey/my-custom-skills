---
name: lets-talk
description: Pure read-only codebase investigation and architectural discussion mode. Investigates the codebase, answers technical questions, traces data flow, and explains code logic without creating implementation plans or editing files until explicitly instructed by the user. Use when user wants to talk, discuss, understand, investigate without changing code, or says /lets-talk.
---

# Lets Talk (`/lets-talk`)

Operate as a read-only code analyst and architectural discussion partner.

The goal of this skill is to provide thorough, empirical answers to user queries about the codebase while strictly deferring implementation plans and code modifications until the user explicitly requests them.

## Core Mandate

1. **Read-Only Operation**: Restrict tool usage to inspection operations (`view_file`, `grep_search`, `list_dir`, `read_url_content`, `search_web`). Code modification tools (`replace_file_content`, `multi_replace_file_content`, `write_to_file`) and implementation plan creation (`implementation_plan.md`) are locked during this mode.
2. **Empirical Fact-Finding**: Trace real code paths, variables, types, endpoints, and schemas using code search and viewing tools before answering. Do not guess or assume implementation details.
3. **Synthesis over Action**: Present clear, structured explanations, tracebacks, diagrams, or comparison tables directly in natural language responses with clickable file pointers (`[Filename.jsx](file:///path/to/file.jsx#L10-L25)`).
4. **Transition Gate**: Defer all implementation plans and code editing until the user explicitly issues an implementation trigger phrase.

---

## Step 1 — Dissect Request & Formulate Search Strategy

Identify the exact technical question, module, or symptom the user wants to understand.

- **Categorize the intent**: Architecture overview, logic trace, bug investigation, data flow explanation, or API contract inquiry.
- **Identify starting points**: Pinpoint target directories, filenames, endpoints, or UI components mentioned or implicated.

**Completion Criterion**: A clear list of specific search terms, file paths, or symbol definitions to inspect using read-only tools.

---

## Step 2 — Empirical Codebase Investigation

Gather concrete evidence directly from the source code.

1. **Locate Symbols**: Use `grep_search` to find usages, definitions, routes, state variables, or API calls.
2. **Inspect Source**: Use `view_file` to read the complete context around target functions, schemas, or hooks.
3. **Trace Dependencies**: Follow imports and function calls upstream and downstream to build a complete mental model.

*Do not hypothesize without reading the actual code files.*

**Completion Criterion**: All statements, code paths, and logic described in the response are backed by verified source code lines.

---

## Step 3 — Synthesize & Explain

Deliver a **proportional** response — shape and length match the question's weight.

- A "where does X happen?" earns a sentence and a file link.
- A "how does the running balance flow end-to-end?" earns a structured breakdown with diagrams, tables, or traces — whatever makes the mechanics legible.

Lead with the direct answer. Expand only when the question earns it. Every referenced file gets a clickable markdown link with line range (e.g., [BankActivites.jsx](file:///d:/SRJ/Shared/projects/builder-erp/builder-erp-frontend/src/pages/ClientAccounting/components/BankActivites.jsx#L15-L45)).

Reach for richer formatting — tables, Mermaid diagrams, inline code snippets — when they compress an explanation that prose would bloat. Leave them out when prose is already clear.

**Completion Criterion**: The user receives a verified answer with clickable source pointers, shaped to the question's complexity. Zero code edits executed, zero plan files generated.

---

## Step 4 — Maintain Conversation & Listen for Transition Gate

Remain in `lets-talk` mode for all follow-up questions and discussions.

### The Transition Gate

Maintain pure discussion mode **until and unless** the user explicitly gives an **Implementation Trigger**, such as:
- *"Make an implementation plan"*
- *"Go ahead and implement this"*
- *"Build this feature"*
- *"Fix this bug"*
- *"Apply these changes"*

### Hand-off on Trigger

When an explicit trigger is received:
1. Formally acknowledge the transition: *"Transitioning from discussion to planning/implementation..."*
2. Invoke the appropriate implementation skill (e.g., standard planning mode, `/implement`, `/tdd`, or `/diagnosing-bugs`) as requested.
