---
name: my-work-report
description: Systematically generate an accurate daily work report for your commits today, saved in .scratch/work-report/. Includes an executive summary, adaptive topic sections, bulk tweak groupings, and a 100% complete citation table with exact line numbers and file:/// links. Use when asked for "my work report", "daily work report", "my changes today", or running /my-work-report.
---

Generate an omission-free work report for commits by the current git user.

## Steps

### 1. Ask date range

Use the `ask_question` tool to present an interactive quiz:

**Question**: "What date range should the work report cover?"
**Options**:
- "Today (since midnight)"
- "Yesterday"
- "Custom date range (I'll specify)"

If the user picks **custom**, ask a follow-up free-text question: "Enter the date range (e.g. '2026-08-05..2026-08-10' or 'last 3 days')."

Map the answer to `--since` / `--after` / `--before` flags for all subsequent git commands:
- **Today**: `--since="midnight"`
- **Yesterday**: `--since="yesterday midnight" --until="midnight"`
- **Custom**: parse into appropriate `--since` and `--until` flags

### 2. Collect commits

```bash
git config user.name
git log --author="$(git config user.name)" <date-flags> --stat --name-status
```

If zero commits, report that and stop.

### 3. Extract the complete file matrix

```bash
git log --author="$(git config user.name)" <date-flags> -p
```

Build the full list of touched files with diff stats. Print the total file count — this is the verification target.

### 4. Analyse and draft

- Write the **executive summary** (2–3 sentences).
- Cluster changes into adaptive topic sections based on module/layer (e.g., UI Components, Backend API, Schema, Config). Use as many or as few sections as the diff warrants.
- **Bulk-tweak grouping**: when 5+ files share a mechanical pattern (import renames, type changes, prop threading, style fixes), write one heading naming the pattern and rationale. Every individual file still gets its own row in the citation table.

### 5. Write the report

Output to `.scratch/work-report/YYYY-MM-DD-<topic-slug>.md`. Derive `<topic-slug>` from commit messages and file paths. For date ranges spanning multiple days, use the end date.

#### Template

```markdown
# Daily Work Report: <Short Topic Title>

- **Date**: YYYY-MM-DD (or date range)
- **Author**: <Git Author Name> (<email>)
- **Branch**: <branch>
- **Commits**: <count> (`<first-hash>..<last-hash>`)
- **Files touched**: <count> (<+lines> additions, <-lines> deletions)

---

## Executive Summary

<2–3 sentences: what was accomplished.>

---

## <Topic Section(s)>

<Narrative with inline [file](file:///path#Lstart-Lend) citations.>

---

## Exhaustive File & Line Citation Table

| File | Action | Lines | +/- | Summary |
|:-----|:------:|:-----:|:---:|:--------|
| [basename](file:///path#Lstart-Lend) | `MODIFIED` | `L10-L45` | +25/−3 | <impact> |
```

The citation table must contain one row per file from step 3 — no omissions.

### 6. Verify

1. Report file exists at the expected path.
2. Citation table row count equals the file count printed in step 3.
3. Every `file:///` link uses forward slashes.
