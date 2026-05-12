---
name: do-commit
description: Use when /do-start has completed one or more tasks in a plan and they need to land as well-scoped git commits — defaults to one commit per completed task, optionally bundles into one commit per plan with explicit user opt-in. Stages only relevant files (never `git add -A`), writes messages that cite the task IDs and plan slug, and updates the plan's handoff note.
argument-hint: plans/<slug>.md [--bundle]
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
---

You are a senior engineer producing clean, reviewable git history for a plan run by `/do-start`. Your job is to translate completed tasks into well-scoped commits — never to write code, never to amend behaviour, never to mark tasks `[x]`.

`/do-start` deliberately does not commit. This skill closes that loop.

## Companion superpowers skills

If superpowers skills are installed, treat them as recommended (not required) companions:

- `superpowers:executing-plans` — same discipline: scoped diffs, no silent scope expansion, traceability between plan and commit.
- `superpowers:requesting-code-review` — once a plan's commits are clean, this is the natural next step.
- `superpowers:finishing-a-development-branch` — when the plan is `done` and committed, this skill decides whether to PR or merge.

These skills are referenced for style and discipline. This skill works without them.

## Shared references

Canonical sources for cross-cutting rules. When they conflict with inline text below, the shared docs win:

- `../_shared/wiki-context.md` — how to read an existing wiki without treating it as authority.
- `../_shared/qmd-readiness.md` — qmd readiness check and Grep/Glob fallback.
- `../_shared/wiki-write-back.md` — when and how to file durable findings back into the wiki.

## Hard gates

- **Never `git add -A` or `git add .`** Stage specific files derived from the task's `Files to modify` plus any genuinely new files the task created.
- **Never commit unrelated working-tree changes.** If `git status` shows files outside the task scope, stop and surface them. Do not include them.
- **Never amend a previous commit unless the user explicitly asks.** Always create new commits.
- **Never skip hooks** (`--no-verify`, `--no-gpg-sign`) unless the user explicitly asks. Fix the underlying issue instead.
- **Never commit secrets** (`.env`, credentials, tokens, key files, large binaries). If a candidate file looks sensitive, stop and ask.

## Red flags

| Thought | Reality |
|---------|---------|
| "Everything looks related, I'll just `git add -A`" | One stray file pollutes git history forever. Stage specifically. |
| "I'll squash all 8 tasks into one commit, simpler" | Bundle is opt-in (`--bundle`). Default is one commit per task so reviewers can map plan ↔ history. |
| "Pre-commit hook failed, I'll just `--no-verify`" | The hook is right; fix the issue and create a new commit. |
| "I'll amend the last commit with this fix" | New commit. Always. Amend only on explicit user request. |

## Input

The plan path is in `$ARGUMENTS`. An optional `--bundle` flag means: produce one commit covering every uncommitted completed task, instead of one commit per task.

---

## Step 1 — Load the plan

1. Parse `$ARGUMENTS` into `<plan path>` and the optional `--bundle` flag.
2. Read the plan file in full.
3. From the YAML front matter, capture `title`, `slug`, and `status`.
4. Build the ordered list of tasks whose status is `[x]` (completed).

## Step 2 — Detect already-committed tasks

For each completed task, decide whether it is *already* represented in git history on the current branch:

1. Run `git log --pretty=%s` (or `--grep`) for the plan slug — commits whose subject mentions `<slug>:` or the task ID.
2. Skip any task whose ID already appears in a commit subject on the current branch.
3. The remaining `[x]` tasks form the **commit queue**.

If the commit queue is empty, report "Nothing to commit — every `[x]` task already has a commit." and stop.

## Step 3 — Inspect the working tree

Run `git status --porcelain=v1`. Bucket every changed/new file:

- **In-scope:** appears in any commit-queue task's `Files to modify`, or is a new file the task obviously produced (verify by reading the task notes and `Files to read`).
- **Out-of-scope:** working-tree changes unrelated to any commit-queue task.

If out-of-scope changes exist, surface them and ask the user:

```
Out-of-scope working-tree changes detected:
  <path>
  <path>

Options:
  a) Stash these and commit only in-scope changes
  b) Include them in the commit (specify which commit)
  c) Cancel — I'll deal with them manually
```

Wait for the user's choice. Do not silently include or discard.

## Step 4 — Build commits

### Default: one commit per task

For each task in commit-queue order:

1. Determine the file set: in-scope files attributable to this task. Files modified by multiple tasks belong to the *first* task that touched them in this session, unless the user directs otherwise.
2. `git add <specific paths>`.
3. Compose the commit message:

   ```
   <slug>: <task title>

   <2-4 sentence summary derived from the task's Notes and the actual diff>

   Task: <task ID> in plans/<slug>.md
   Verify: <verify command, if defined>
   ```

   Keep the subject line under 72 characters. Pass the full message via HEREDOC.

4. Run `git commit -m "$(cat <<'EOF' ... EOF)"`.
5. If the pre-commit hook fails:
   - Read the hook output.
   - Fix the issue.
   - Re-stage and create a NEW commit (do NOT `--amend` — the previous commit did not happen).
6. Confirm with `git log -1 --pretty=oneline`.

### `--bundle` flag: one commit per plan

If the user passed `--bundle`:

1. Stage every in-scope file across the commit queue.
2. Compose a single commit message:

   ```
   <slug>: <high-level summary derived from plan title>

   <Bulleted task list — one bullet per committed task ID + title>

   Plan: plans/<slug>.md
   Tasks: <comma-separated task IDs>
   ```

3. Commit once. Same hook-failure discipline as above.

## Step 5 — Update the plan's handoff note

Overwrite the `## Handoff notes` section to reflect what was committed this session. Append a brief decisions log entry:

```
YYYY-MM-DD — /do-commit produced <N> commit(s) for <task IDs>. Mode: per-task | bundle.
```

Do not change task statuses. Do not change front-matter `status` (`/do-start` owns that).

## Step 6 — Report back

```
Committed <N> change(s) on <branch>:

  <short-sha>  <slug>: <task title>           [T3]
  <short-sha>  <slug>: <task title>           [T4]
  ...

Plan: plans/<slug>.md (status: <front-matter status>)
Out-of-scope changes: <list or "none">
Skipped tasks (already committed): <list or "none">

Next step:
  - If the plan is `in-progress`: continue with /do-start.
  - If the plan is `done`: consider /gsd-ship or `superpowers:finishing-a-development-branch`.
```

## Rules

- One commit per `[x]` task by default; `--bundle` to combine.
- Stage specific paths only — never `-A` or `.`.
- Subject line ≤ 72 chars, plan slug prefix, task ID(s) in the trailer.
- Never amend or force-push.
- Never skip hooks.
- Do not modify task statuses or front-matter `status`.
- If a wiki exists, this skill does not write to it. Commits are not durable wiki content.
