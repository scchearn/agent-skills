---
title: <Feature Name>
slug: <feature-slug>
description: <Short plan summary, 70 tokens max>
status: pending # pending | in-progress | done | abandoned
task_count: 0
created_at: YYYY-MM-DD HH:MM
started_at: null
completed_at: null
---

# <Feature Name>

## Related research

<!-- Link only the research memos actually used while planning. -->

_None linked._

## Related specs

<!-- Link only the spec files actually used while planning. -->

_None linked._

## Goal

<One sentence: what does "done" look like? What is the observable end state?>

## Acceptance criteria

- [ ] Primary observable outcome works as described
- [ ] Relevant automated tests or validations covering the changed behavior pass
- [ ] Relevant workspace-native validation commands pass (for example: test, typecheck, lint, build)

## Tasks

<!-- Status key: [ ] pending | [~] in-progress | [h] delegated to hcom | [x] done | [!] blocked | [>] needs re-run -->
<!-- Each task must be atomic (roughly 1–20 files), verifiable, and dependency-aware. -->
<!-- "Depends on" references task IDs (T1, T2, etc.) by their heading. -->
<!-- "Execution" is optional. Prefer: topology: <topology> | agent: <tag> | worktree: <path> | branch: <name> | model: <provider/model> | rules: <behavioral rules>. Use "same agent as Tn" for continuation tasks. Advisory-only topology text is allowed, but /do-start may fall back to inline execution when concrete launch fields are missing. -->

### T1 — <title>

- **Status:** [ ]
- **Depends on:** none
- **Execution:** <!-- Optional. Prefer: topology: <topology> | agent: <tag> | worktree: <path> | branch: <name> | model: <provider/model> | rules: <behavioral rules>. Use "same agent as Tn" for continuation tasks. Remove this line if single-agent execution is appropriate. -->
- **Verify:** `<focused workspace-native automated command; avoid vague entries like "run tests">`
- **Files to read:** <!-- concrete workspace-relative paths only; include research memos, docs, source files, tests, contracts, or examples. Do not write vague placeholders like "relevant files". -->
- **Files to modify:** <!-- concrete workspace-relative paths only; list every file expected to change or be created. Do not write vague placeholders like "implementation files". -->
- **Notes:** <!-- 1-3 bullets describing what this task delivers, key constraints, and any required follow-through. Do not leave blank or write TBD. -->

### T2 — <title>

- **Status:** [ ]
- **Depends on:** T1
- **Execution:** <!-- Optional. Use the same structured format as T1, or "same agent as T1" / "same agent as Tn" for continuation tasks. Remove if single-agent. -->
- **Verify:** `<focused workspace-native automated command; avoid vague entries like "run tests">`
- **Files to read:** <!-- concrete workspace-relative paths only; include research memos, docs, source files, tests, contracts, or examples. Do not write vague placeholders like "relevant files". -->
- **Files to modify:** <!-- concrete workspace-relative paths only; list every file expected to change or be created. Do not write vague placeholders like "implementation files". -->
- **Notes:** <!-- 1-3 bullets describing what this task delivers, key constraints, and any required follow-through. Do not leave blank or write TBD. -->

---

## Decisions log

<!-- APPEND-ONLY. Never edit or delete existing entries. -->
<!-- Format: `YYYY-MM-DD — <decision and rationale>` -->

---

## Handoff notes

<!-- Overwritten at the end of each execution session by /do-start. -->

_No handoff yet._
