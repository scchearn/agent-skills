---
name: do-abandon
description: Use when a plan should be stopped permanently — rolls plan status to abandoned with explicit user confirmation, marks any in-progress task `[!]`, appends an append-only decisions-log entry capturing the reason, and moves the row in plans/INDEX.md to the abandoned section. Does not delete code, does not delete the plan file, and is recoverable only by manual edit.
argument-hint: plans/<slug>.md # describe the reason in your message, then invoke this skill
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Glob, Grep
---

You are a senior engineer ending an in-flight plan. Your job is to flip the plan to `abandoned` cleanly, preserve history, and never silently lose context. Abandoning is a deliberate, reversible-by-edit operation — not deletion.

## Companion superpowers skills

If superpowers skills are installed, treat them as recommended (not required) companions:

- `superpowers:brainstorming` — if the user is uncertain whether to abandon or pivot, run brainstorming first. Abandoning is a one-way (manual to undo) door.
- `superpowers:writing-plans` — once abandoned, if a replacement plan is needed, hand off to `/do-plan` rather than reviving this one.

These skills are referenced for style and discipline. This skill works without them.

## Shared references

Canonical sources for cross-cutting rules. When they conflict with inline text below, the shared docs win:

- `../_shared/wiki-context.md` — how to read an existing wiki without treating it as authority.
- `../_shared/qmd-readiness.md` — qmd readiness check and Grep/Glob fallback.
- `../_shared/wiki-write-back.md` — when and how to file durable findings back into the wiki.

## Hard gates

- **No file edits before user confirms abandonment.** Phases 1–2 are read and reason only. Even an "obvious" decision waits for explicit yes.
- **Never delete the plan file.** Abandoned plans stay in `plans/` as part of the audit trail.
- **Never delete or rewrite completed tasks.** `[x]` tasks remain `[x]` — they happened, even if the plan they belonged to is being stopped.
- **Never delete history.** The decisions log is append-only; record the abandonment as an entry, do not edit prior entries.

## Red flags

| Thought | Reality |
|---------|---------|
| "User said 'abandon', that's confirmation enough" | The argument is the *input*, not approval. Surface the proposal and wait for explicit yes. |
| "I'll delete this plan to keep `plans/` tidy" | Never. Abandoned plans stay on disk. |
| "I'll roll the `[x]` tasks back to `[ ]` since they don't matter now" | They happened. Leave them `[x]`. |
| "We can amend this back to in-progress later" | Abandonment is final-by-default. If revival is likely, recommend `/do-amend` instead. |

## Input

The plan path is in `$ARGUMENTS`. The reason for abandoning is in the conversation context immediately above this prompt. Read it carefully before doing anything else.

---

## Phase 1 — Deep read

Read the plan file in full:

1. Front matter: `title`, `slug`, `description`, `status`, `task_count`, `created_at`, `started_at`, `completed_at`.
2. All tasks: ID, title, status, dependencies. Note which are `[ ]`, `[~]`, `[x]`, `[!]`, `[>]`.
3. Decisions log: read so the abandonment entry can reference relevant prior decisions.
4. Handoff notes: read so the abandonment summary can capture last-known state.

Do not modify anything yet.

## Phase 2 — Build the abandonment proposal

Compose, but do not apply yet:

```
## Abandonment proposal for plans/<slug>.md

### Reason
<1-3 sentences taken from the user's prompt + your own read of the plan>

### Current state
- Status (front matter): <current status>
- Completed tasks: T1, T2, ... (or "none")
- In-progress tasks: T3 (will be marked [!]) (or "none")
- Pending tasks: T4, T5, ... (will remain [ ]) (or "none")
- Blocked/needs-rerun tasks: T6 [!], T7 [>] (or "none")

### What will change on disk
- Front matter: status → abandoned; completed_at → today; preserve created_at, started_at.
- Any [~] task → [!] with note "Abandoned mid-task on YYYY-MM-DD: <reason>"
- Decisions log: append exactly one entry capturing the reason.
- plans/INDEX.md: move row to the abandoned section.
- Plan file itself is not deleted. Already-committed code is not reverted.

### What will NOT change
- [x] tasks stay [x]. They happened.
- [ ], [>] tasks stay [ ], [>]. They will not run again unless the plan is manually reopened.
- No code or git history is modified by this skill.

### Anything you want to add to the decisions log?
<Surface any extra context you want recorded permanently>
```

## Phase 3 — Confirm with user

**STOP HERE. Do not write any files.**

Present the proposal above, then ask:

> "Confirm abandonment of plans/<slug>.md? If yes, I'll apply the changes above. If anything should be added or removed, tell me and I'll revise before applying."

Wait for explicit confirmation (e.g. "yes", "confirm", "apply"). If the user wants changes, revise the proposal and ask again. Do not proceed without an explicit yes.

## Phase 4 — Apply

Once confirmed, in this order:

### A. Mark in-progress tasks `[!]`

For each task with status `[~]`:

1. Change `- **Status:** [~]` to `- **Status:** [!]`.
2. Append to the task's Notes: `Abandoned mid-task on YYYY-MM-DD: <short reason>.`

Do not touch other task statuses.

### B. Append to the decisions log

Add exactly one entry:

```
YYYY-MM-DD — Plan abandoned. Reason: <short reason>. Completed at abandonment: <task ID list or "none">. Mid-task at abandonment: <task ID list or "none">.
```

The log is append-only — do not modify prior entries.

### C. Update front matter

- `status: abandoned`
- `completed_at: YYYY-MM-DD HH:MM` (use the abandonment timestamp; this records when the plan stopped, not when it was finished)
- Preserve `created_at`, `started_at`, `task_count`, `slug`, `title`, `description`.
- Remove any legacy `updated_at` and any legacy `## Plan summary` section if still present.

### D. Update `plans/INDEX.md`

- If the INDEX still uses the legacy timestamp-heavy schema, rewrite it to the slim `Status | Title | Plan | Description | Tasks` schema first.
- Update the row for this plan: `Status` → `abandoned`.
- Move the row into the abandoned section (after `done`).
- Preserve the slug link, description, and task count.

### E. Overwrite handoff notes

Replace the `## Handoff notes` section with a final note:

```
## Handoff notes

**Plan abandoned:** YYYY-MM-DD
**Reason:** <short reason>
**Completed before abandonment:** T1, T2, ... or "none"
**Mid-task at abandonment:** T3 (marked [!]) or "none"
**Recovery:** To revive, manually edit front matter status back to in-progress and remove the abandoned-section row from plans/INDEX.md. This skill will not auto-revive.
```

## Phase 5 — Report

```
Plan abandoned: plans/<slug>.md

Front matter status: abandoned
Marked [!] mid-task: <task IDs or "none">
Decisions log: one entry appended
INDEX.md: row moved to abandoned section

The plan file is preserved as an audit record. Already-committed code is unaffected.
To revive, manually edit the front matter and INDEX row. There is no /do-revive skill.
```

## Rules

- Abandon is a deliberate, recoverable-only-by-manual-edit action.
- Plan files and `[x]` task records are never deleted.
- The decisions log is append-only; abandonment is one new entry, not a rewrite.
- Do not touch git history; do not revert code.
- `plans/INDEX.md` and front matter must stay synchronized.
- No wiki write-back from this skill — abandonments are plan-management chatter, not durable knowledge.
