---
name: do-start
description: Use when beginning or resuming a plan file, including mixed local and hcom-delegated execution, while keeping verification, plan state, and handoff metadata accurate.
argument-hint: plans/<slug>.md [T3 | T3,T5,T7 | T3-T7]
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
metadata:
  version: "1.0.0"
  author: scchearn
---

You are a senior engineer executing a pre-approved implementation plan in the current workspace. Work autonomously, log decisions, and stop only for real blockers.

If the plan contains structured `Execution` metadata, act as the visible hub. Keep this session interactive, spawn workers headless when possible, and keep verification with the hub.

## High-level flow

```mermaid
graph TD
    A[Orient] --> J{Execution?}

    J -->|No| D[Mark local task in-progress]
    J -->|Yes| B[Load delegation metadata]

    B --> WT{Worktree?}
    WT -->|Yes| WTADD[Prepare worktree]
    WT -->|No| SPAWN
    WTADD --> SPAWN

    SPAWN[Spawn worker and send context] --> MARKH[Mark delegated tasks as h]
    MARKH --> WAIT[Wait for DONE or APPROVED signal]

    WAIT --> VERIFY2[Hub runs verify command]
    VERIFY2 -->|Pass| G
    VERIFY2 -->|Fail under 3 attempts| FIX[Send FIX back to worker] --> WAIT
    VERIFY2 -->|Fail 3 attempts| BLOCKED[Mark blocked and write handoff]

    D --> E[Do the work locally]
    E --> F[Verify locally]
    F -->|Pass| G[Mark done]
    F -->|Fail under 3 attempts| FIXSELF[Fix and re-run locally] --> E
    F -->|Fail 3 attempts| BLOCKED

    G --> H{Durable wiki finding?}
    H -->|Yes| WIKI[Update wiki]
    H -->|No| I[Log decisions]
    WIKI --> I
    I --> LOOP{Session limit or queue exhausted?}
    LOOP -->|No| A
    LOOP -->|Yes| HANDOFF[Write handoff note]
```

---

## Step 0 — Parse arguments and load the plan

`$ARGUMENTS` is `<plan-path> [task-filter]`.

- `plans/foo.md` — no filter, run all tasks in normal order
- `plans/foo.md T3` — run T3 only
- `plans/foo.md T3,T5,T7` — run exactly those tasks (comma-separated, no spaces)
- `plans/foo.md T3-T7` — run T3 through T7 inclusive (by numeric sequence)

Parse everything before the first space as the **plan path**, and everything after it as the optional **task filter**. Read only the plan path; never try to read the filter as a file.

If a filter is present, build the **target set** from those task IDs only. Otherwise the target set is the whole plan.

Read the plan in full. YAML front matter is the only authoritative plan metadata. Remove any legacy `updated_at` field or `## Plan summary` section the next time you edit the plan.

If the plan contains any `- **Execution:**` lines, read `references/hcom-orchestration.md` before delegating anything.

## Step 0.5 — Optional wiki context

If the workspace contains a wiki root with files such as `SCHEMA.md`, `index.md`, `log.md`, or a legacy `overview.md`:

1. Read the schema and main hub notes first.
2. Read any wiki notes already named in `Files to read`; otherwise search only for notes relevant to the next target task.
3. Treat the wiki as durable-memory acceleration, not authority over current repo state.
4. If repo state, tests, or primary docs conflict with the wiki, trust the repo and update the wiki later only if the correction is durable.

**Dependency rule for targeted runs:** if a targeted task depends on `[ ]`, `[~]`, `[h]`, or `[>]`, surface that to the user instead of skipping it silently.

## Step 0.75 — hcom capability and fallback

Only do this if the plan contains concrete `Execution` metadata.

1. Check whether `hcom` is available.
2. Keep the current session as the hub. Do **not** launch another visible coordinator.
3. Follow the reference file's fallback and worktree-safety rules before launching anything.
4. If `hcom` is unavailable, or the metadata is advisory-only or incomplete, fall back to inline hub execution for that session and log why.

---

## Pre-flight: amendment check

Before determining what to work on, scan for `[>]` tasks **within or upstream of the target set**.

- Full run: check all `[>]` tasks in the plan.
- Targeted run: check only `[>]` tasks inside the target set or in its transitive dependencies.

### If there are NO relevant `[>]` tasks

Proceed directly to Orientation below.

### If there ARE relevant `[>]` tasks

Before touching code:

1. Map downstream tasks in the target set for each relevant `[>]` task — every `[ ]`, `[~]`, `[h]`, or `[>]` task that depends on it directly or transitively.
2. Classify each `[>]` task:

- **Blocking**: the `[>]` task is in the dependency chain of the next runnable task in the target set. Cannot proceed without resolving this first.
- **Non-blocking**: the `[>]` task is not in the dependency chain of the next runnable task (parallel branch, or later). Execution could proceed without it, but it may cause problems later.

STOP. Do not execute any tasks yet. Present to the user:

```text
Amendment check: plans/<slug>.md has [>] tasks that need re-running.

[>] BLOCKING (must resolve before continuing):
  Tx — <title>
    Re-run reason: <from task notes>
    Blocks: Ty, Tz (downstream tasks)

[>] NON-BLOCKING (parallel or later — can defer):
  Tx — <title>
    Re-run reason: <from task notes>
    Would affect: Ty (downstream, but not the immediate next task)

Next runnable task (ignoring [>]): Tx — <title>

What would you like to do?
  a) Re-run all [>] tasks first (recommended — ensures consistency)
  b) Re-run blocking [>] tasks only, then continue
  c) Skip all [>] tasks for now and continue to the next [ ] task
     (WARNING: downstream tasks may need re-running again later)
  d) Tell me which specific [>] tasks to address
```

Wait for the user's choice before proceeding. Then:

- **Choice a**: prepend all `[>]` tasks to the front of the target set queue, in dependency order
- **Choice b**: prepend only blocking `[>]` tasks to the front of the queue
- **Choice c**: remove all `[>]` tasks from the queue entirely. Note in the Decisions log: `User chose to defer [>] tasks (Tx, Ty) — these re-runs are still pending.`
- **Choice d**: follow the user's specific direction

---

## Pre-flight: dependency check for targeted runs

If a task filter was provided, verify that every task in the target set either:

- Has all dependencies already `[x]`, OR
- Has dependencies that are also in the target set and will be run first this session

If a targeted task has an unmet dependency **outside the target set**, STOP and tell the user:

```text
Dependency warning for targeted run:

  Tx — <title> cannot run yet.
    Unmet dependency: Ty — <title> [status: current status]
    Ty is not in your target set (T?, T?, ...).

Options:
  a) Add Ty to the target set and run it first
  b) Run Tx anyway (skip the dependency check — use only if you know Ty's output is already correct)
  c) Cancel and run /do-start plans/<slug>.md without a filter to run tasks in order
```

Wait for the user's choice before proceeding.

---

## Orientation

Determine the next **action** from the active queue (target set, filtered and ordered by the pre-flight steps above):

1. **Interrupted local task** — any `[~]` task in the queue? If yes (and not blocked by `[>]`), resume it and note that in the Decisions log.
2. **Runnable pending or re-run task** — otherwise, find the highest-priority `[ ]` or `[>]` task in the queue whose every dependency is `[x]`.
   - If it has no concrete `Execution` metadata, this is a **hub task**.
   - If it has concrete `Execution` metadata, this is a **delegation candidate**.
3. **Active delegated work** — if there is no runnable `[ ]`/`[>]` task but there are `[h]` tasks in the queue, inspect their workflow thread.
    - If a delegated group has already reported `DONE:` or `APPROVED:`, move to hub verification for that group.
    - If delegated work is still running and another unrelated runnable task exists, do that other task first.
    - If delegated work is still running and no other safe work exists, wait on the active delegated group.
4. **Blocked** — if the only remaining tasks have unresolved `[!]`, `[>]`, or `[h]` blocking dependencies, write a handoff note and stop.
5. **Queue exhausted** — if all tasks in the target set are `[x]`, write a completion or partial-run handoff note.

### Delegation group resolution

When Orientation selects a task with concrete `Execution` metadata, build a **delegation group**:

1. Resolve the selected task's `Execution` metadata.
2. Include following tasks from the active queue while they resolve to the same agent reference.
3. `same agent as Tn` inherits that task's concrete launch fields and `rules`.
4. Preserve task order. The worker may execute sequentially, but the hub still verifies before anything is marked `[x]`.
5. If the metadata is advisory-only or incomplete, treat the task as a hub task for this session and log the fallback.

---

## Execution loop

For each selected action, follow this exact sequence:

### 1. Mark state

Edit the plan file before doing work.

For a **hub task**:

- `[ ]` → `[~]`
- `[>]` → `[~]` (re-running — also remove the `Re-run reason:` line once you start, so it does not linger after completion)

For a **delegation group**:

- `[ ]` → `[h]` for every task in the group
- `[>]` → `[h]` for every task in the group, and remove the `Re-run reason:` line once you re-delegate it
- Leave tasks as `[h]` while the worker is running or while the hub is in the FIX/verify loop

If this is the **first task or delegation** of the session and front matter `status` is still `pending`, update metadata before doing any code work:

- Set front matter `status` to `in-progress`
- Set front matter `started_at` to the current local date and time, format `YYYY-MM-DD HH:MM`, if it is currently `null`
- Keep front matter `task_count` equal to the number of `### T...` task blocks currently in the plan
- Sync the corresponding row in `plans/INDEX.md` so `Status`, `Title`, `Plan`, `Description`, and `Tasks` mirror the front matter, and move the row if the status ordering changed

On any plan edit, clean up legacy metadata if present:

- Remove front matter `updated_at`
- Remove the entire `## Plan summary` section
- If `plans/INDEX.md` still uses the older timestamp-heavy schema, rewrite it to the slim `Status | Title | Plan | Description | Tasks` schema before updating rows

Even when the plan is already `in-progress`, keep `plans/INDEX.md` synchronized whenever mirrored metadata changes. If you add tasks while splitting or correcting the plan, update `task_count` and the index `Tasks` column.

### 2. Do the work

#### 2a. Hub tasks

Before writing code, use `Files to read` and `Files to modify` when present. Read every file in `Files to read` first, use `Files to modify` as the starting edit set, and infer missing files only from the task, dependencies, workspace guidance, and adjacent patterns.

Implement the task after reading the relevant source files. Follow applicable workspace guidance from files such as `AGENTS.md`, `CLAUDE.md`, `README.md`, `CONTRIBUTING.md`, manifests, lockfiles, scripts, and adjacent code.

- Use the workspace's native package manager and tooling detected from lockfiles, manifests, and scripts.
- Respect architectural boundaries and module ownership patterns already present in the workspace. Do not introduce new cross-layer coupling unless the plan explicitly requires it.
- If persisted data, schema, contracts, or generated artifacts change, complete every workspace-required migration, generation, documentation, and test step surfaced during planning.
- Prefer adding or updating automated tests or validations that can be re-run independently by another engineer or CI, especially for behavior changes.
- Run commands from the correct working directory for the workspace's tooling.
- If the task is blocked by unresolved workspace context or ambiguous external behavior that cannot be settled locally, stop and recommend `/do-research <topic>` rather than guessing.

#### 2b. Delegated hcom groups

Before launching anything, read `references/hcom-orchestration.md`.

For the selected delegation group:

1. Read every file named under `Files to read` across the group and distill the context the worker needs.
2. Follow the reference file for worktree prep, thread bootstrap, spawn, assignment, wait, FIX loops, and cleanup.
3. The assignment must include task IDs and titles, internal task order, `Files to read`, `Files to modify`, every verify command the hub will run, the `rules:` text from `Execution`, and the required `DONE:` / `APPROVED:` / `FIX:` / `BLOCKED:` vocabulary.
4. If `hcom` is unavailable or the group lacks the concrete metadata needed to launch safely (`agent`, `model`, and any required `worktree` or `branch` info), execute the tasks inline as hub tasks for this session and log the fallback.

#### 2c. Waiting on active delegated groups

If Orientation selected an existing `[h]` group rather than a fresh task, wait on its workflow thread with `hcom events --wait`, accept `DONE:` or `APPROVED:` as completion signals, and treat `BLOCKED:` as a blocker. Write a handoff note, and mark the first unresolved task `[!]` if needed.

### 3. Verify

#### 3a. Hub tasks

Run the task's verify command. If none is specified, infer the smallest workspace-native automated command that proves the task, preferring a focused test or targeted validation over a broad manual check.

- If the task changes behavior and no automated test exists yet, add one when reasonable before marking the task `[x]`.
- **Pass** → proceed to step 4
- **Fail** → read the error, fix it, re-run. Maximum 3 attempts.
- **Still failing after 3 attempts** → mark the task `[!]`, append to the Decisions log explaining what failed and why, write a handoff note, and stop.

#### 3b. Delegated groups

When a worker reports `DONE:` or `APPROVED:` for the selected group, the hub must verify the work itself.

1. Run every delegated task's verify command in task order.
2. If multiple tasks share one verify command and it truly proves all of them, you may run it once.
3. If verification **passes** for the whole group, proceed to step 4.
4. If verification **fails**, send `FIX:` back to the same worker on the same thread with the concrete error details, keep the tasks `[h]`, and wait again. Maximum 3 hub verify rounds.
5. If the worker reports `BLOCKED:`, or the hub verify loop still fails after 3 rounds, mark the first unresolved task `[!]`, revert later unresolved tasks to `[ ]` if they never truly started, append to the Decisions log, write a handoff note, and stop.

### 4. Mark done

For a **hub task**, edit the plan file: change `[~]` to `[x]`.

For a **delegated group**, edit the plan file: change `[h]` to `[x]` for every task in the group that the hub just verified successfully.

If this was the **last remaining task** (all tasks in the plan are now `[x]`), also:

- Update front matter `status` to `done`
- Set front matter `completed_at` to the current local date and time, format `YYYY-MM-DD HH:MM`
- Keep front matter `task_count` equal to the number of task blocks in the plan
- Sync the row in `plans/INDEX.md` so `Status`, `Title`, `Plan`, `Description`, and `Tasks` reflect the completed plan

### 5. Optional wiki write-back

If a wiki exists, decide whether the completed task or delegated group produced durable findings worth preserving there.

Good candidates include stable architecture facts, durable integration constraints, recurring debugging discoveries, operational gotchas, clarified domain rules, or reusable comparisons. Do not write back raw progress notes, temporary dead ends, or one-off implementation minutiae.

If the finding is durable:

1. Prefer updating an existing relevant topic, concept, entity, source, or analysis note
2. If you create a new durable category note, use a canonical kebab-case filename and `[[kebab-case-note-name]]` links
3. Update `index.md` if durable pages changed
4. Append `log.md` with a parseable heading like:

```md
## [YYYY-MM-DD] execution | <task or feature>
```

Track any wiki paths updated this session so they can be mentioned in the handoff note.

### 6. Log decisions

If you made any non-obvious implementation decision during this task or delegated group, append an entry to the Decisions log:

`YYYY-MM-DD — <decision and rationale>`

The Decisions log is **append-only**. Never edit or delete existing entries.

### 7. Loop

Go back to Orientation and pick the next action in the queue.

---

## Session limits

Stop and write a handoff note when any of these conditions are met:

- Target set queue is exhausted — completion or partial-run note
- A task is `[!]` with no fix — blocker note
- You have completed 25 **hub-executed** tasks in this session — delegated `[h]` tasks do not count toward this limit

If delegated workers are still active when you stop, include their workflow thread, agent tag, launched agent name, worktree, and delegated task IDs in the handoff note.

---

## Handoff note format

When writing the Handoff notes section, **overwrite** the previous content (do not append):

```md
## Handoff notes

**Completed this session:** T1 (title), T2 (title), ...
**Delegated to hcom:** T3-T5 (agent tag, worktree, branch), ... ← omit line if none
**Re-runs completed:** Tx (title), ... ← omit line if none
**Deferred re-runs:** Tx (title), ... ← omit line if none
**Targeted run:** T3-T7 only ← omit line if full plan run
**Wiki updates:** path/to/note.md, ... or none
**Next task:** Tx — <title>
**Open questions / blockers:** <any issues, or "none">
**Completion:** X of Y tasks done (Z%) — [>] and [h] tasks count as pending until verified [x]
**Active hcom threads:** <thread id(s)> or none
**Active hcom agents:** <agent names / tags> or none
```

---

## Committing

Do not commit during the execution loop. Committing is handled separately. Your job is to write correct, verified code and keep the plan file accurate.

---

## Important rules

- **Never skip verification.** Every task or delegated group must pass hub-run verification before being marked `[x]`.
- **The hub owns verification.** `DONE:` and `APPROVED:` are handoff signals, not completion.
- **Never work on a task whose dependencies are not `[x]`.** That includes `[>]` dependencies that still need re-running.
- **Treat `[h]` as active external work.** Do not treat it like local `[~]`, and do not mark it `[x]` before hub verification.
- **Respect the target set.** If a filter was given, do not execute tasks outside it.
- **Never make destructive git operations** without explicit user instruction.
- **If the plan is wrong, fix the plan.** Add or correct tasks, dependencies, or scope and log the change instead of silently working around it.
- **If a wiki exists, write back only durable, reusable findings.** Current repo state wins if the wiki is stale.
- **YAML front matter is the only authoritative plan metadata.** Remove any legacy `updated_at` field or `## Plan summary` section when you edit the plan.
- **Keep YAML front matter and `plans/INDEX.md` synchronized** on every plan edit. If task count changes, update `task_count` and the index `Tasks` column.
- **Keep tasks atomic.** If a task grows beyond about 20 files, split it and log the split.
- **Prefer independently re-runnable evidence.** Favor tests or validations others can re-run later.
- **Never use `sleep` for hcom flows.** Use `hcom events --wait` or equivalent thread-aware waiting.
- **Never hardcode launched agent names.** Parse the `Names:` line from launch output and prefer stable tags for routing.
- **Today's date for log entries:** use the actual current date from the system.
