---
title: <Feature Name>
slug: <feature-slug>
description: <Short plan summary, 70 tokens max>
status: pending # pending | in-progress | done | abandoned
task_count: 0
created_at: YYYY-MM-DD HH:MM
updated_at: YYYY-MM-DD HH:MM
started_at: null
completed_at: null
---

# <Feature Name>

## Plan summary

<!-- Keep this section in sync with the YAML front matter above. -->

- **Description:** <Short plan summary, 70 tokens max>
- **Status:** `pending` <!-- pending | in-progress | done | abandoned -->
- **Slug:** `<feature-slug>`
- **Task count:** `0`
- **Created:** `YYYY-MM-DD HH:MM`
- **Last updated:** `YYYY-MM-DD HH:MM`
- **Started:** `not started`
- **Completed:** `not completed`

## Related research

<!-- Link only the research memos actually used while planning. -->

_None linked._

## Goal

<One sentence: what does "done" look like? What is the observable end state?>

## Acceptance criteria

- [ ] Primary observable outcome works as described
- [ ] Relevant automated tests or validations covering the changed behavior pass
- [ ] Relevant workspace-native validation commands pass (for example: test, typecheck, lint, build)

## Tasks

<!-- Status key: [ ] pending | [~] in-progress | [x] done | [!] blocked | [>] needs re-run -->
<!-- Each task must be atomic (roughly 1–20 files), verifiable, and dependency-aware. -->
<!-- "Depends on" references task IDs (T1, T2, etc.) by their heading. -->

### T1 — <title>

- **Status:** [ ]
- **Depends on:** none
- **Verify:** `<focused workspace-native automated check>`
- **Files to read:** <!-- research memos, docs, existing source files, tests, contracts, or other references to consult before starting this task -->
- **Files to modify:** <!-- source files this task will create or change -->
- **Notes:**

### T2 — <title>

- **Status:** [ ]
- **Depends on:** T1
- **Verify:** `<focused workspace-native automated check>`
- **Files to read:** <!-- research memos, docs, existing source files, tests, contracts, or other references to consult before starting this task -->
- **Files to modify:** <!-- source files this task will create or change -->
- **Notes:**

---

## Decisions log

<!-- APPEND-ONLY. Never edit or delete existing entries. -->
<!-- Format: `YYYY-MM-DD — <decision and rationale>` -->

---

## Handoff notes

<!-- Overwritten at the end of each execution session by /do-start. -->

_No handoff yet._
