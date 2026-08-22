---
name: team-work-report
description: Systematically generate an accurate team work report for all activity on the active branch today across all contributors, saved in .scratch/work-report/. Includes contributor breakdown, executive summary, adaptive topic sections, bulk tweak groupings, and a 100% complete citation table with exact line numbers and file:/// links. Use when asked for "team work report", "branch work report", "branch changes today", or running /team-work-report.
---

Generate an omission-free team work report for all commits on the active branch.

## Steps

### 1. Ask date range

Use the `ask_question` tool to present an interactive quiz:

**Question**: "What date range should the team work report cover?"
**Options**:
- "Today (since midnight)"
- "Yesterday"
- "Custom date range (I'll specify)"

If the user picks **custom**, ask a follow-up free-text question: "Enter the date range (e.g. '2026-08-05..2026-08-10' or 'last 3 days')."

Map the answer to `--since` / `--after` / `--before` flags for all subsequent git commands:
- **Today**: `--since="midnight"`
- **Yesterday**: `--since="yesterday midnight" --until="midnight"`
- **Custom**: parse into appropriate `--since` and `--until` flags

### 2. Collect commits and contributors

```bash
git rev-parse --abbrev-ref HEAD
git log <date-flags> --stat --name-status
git shortlog -sn <date-flags>
```

If zero commits, report that and stop.

### 3. Extract the complete file matrix

```bash
git log <date-flags> -p
```

Build the full list of touched files with diff stats and per-file author attribution. Print the total file count — this is the verification target.

### 4. Analyse and draft

- Write the **executive summary** (2–3 sentences of collective progress).
- Write the **contributor breakdown** (author, commit count, primary focus).
- Cluster changes into adaptive topic sections based on module/layer (e.g., UI Components, Backend API, Schema, Config). Use as many or as few sections as the diff warrants.
- **Bulk-tweak grouping**: when 5+ files share a mechanical pattern (import renames, type changes, prop threading, style fixes), write one heading naming the pattern and rationale. Every individual file still gets its own row in the citation table.

### 5. Write the report

Output to `.scratch/work-report/YYYY-MM-DD-team-<topic-slug>.md`. Derive `<topic-slug>` from commit messages and file paths. For date ranges spanning multiple days, use the end date.

#### Template

```markdown
# Team Work Report: <Short Topic Title>

- **Date**: YYYY-MM-DD (or date range)
- **Active Branch**: `<branch>`
- **Contributors**: <count> (<names>)
- **Commits**: <count> (`<first-hash>..<last-hash>`)
- **Files touched**: <count> (<+lines> additions, <-lines> deletions)

---

## Executive Summary

<2–3 sentences: collective progress.>

---

## Contributor Breakdown

- **<Author>**: <count> commits — <primary focus>

---

## <Topic Section(s)>

<Narrative with inline [file](file:///path#Lstart-Lend) citations.>

---

## Exhaustive File & Line Citation Table

| File | Contributor | Action | Lines | +/- | Summary |
|:-----|:-----------|:------:|:-----:|:---:|:--------|
| [basename](file:///path#Lstart-Lend) | <Author> | `MODIFIED` | `L10-L45` | +25/−3 | <impact> |
```

The citation table must contain one row per file from step 3 — no omissions.

### 6. Verify

1. Report file exists at the expected path.
2. Citation table row count equals the file count printed in step 3.
3. Every `file:///` link uses forward slashes.
