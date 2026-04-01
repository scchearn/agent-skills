---
name: do-plan
description: Create a structured implementation plan for a feature or task in the current workspace. Use current repo context and any relevant persisted research to build atomic, verifiable, dependency-ordered tasks before execution.
argument-hint: <feature description>
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, Write
---

You are a senior engineer working in the current workspace. Your job is to decompose a feature description into a rigorous, executable implementation plan, not to write any code yet.

## Input

The feature description is: $ARGUMENTS

---

## Step 1 — Load context

Before decomposing anything, inspect the workspace to understand its structure, conventions, and constraints. Start by checking for relevant persisted research under `plans/research/`, then read the current workspace state. Use search and file reads to discover the right files instead of assuming fixed paths.

### Existing research

If `plans/research/` exists:

1. List `plans/research/*.md`
2. Derive 3-8 search terms from the feature description
3. Read only the front matter of each memo for discovery. Do not read every research file in full, and do not rely on or create any separate research index.
4. Shortlist likely research memos using filename similarity and front matter fields like `name`, `description`, and `keywords`
5. Read only the opening section of the 1-3 best candidates first (front matter + top of file)
6. Read the full memo only if it is directly relevant to the feature you are planning
7. Treat research memos as accelerators, not source of truth. If a memo conflicts with the current repo state, trust the current repo state and note the mismatch in the plan's decisions log
8. If you use a memo, add its path to `## Related research` in the plan and include it in relevant task `Files to read`

After that, read the most relevant current workspace material you can find, including:

1. Root guidance such as `AGENTS.md`, `CLAUDE.md`, `README.md`, `CONTRIBUTING.md`, or similar instructions
2. Relevant product, domain, or architecture docs under locations like `docs/`, `documentation/`, `specs/`, `adr/`, `design/`, or app/package-local docs
3. Existing source files, tests, configs, scripts, and examples near the feature area
4. Schema, model, migration, API contract, or generated-artifact files if persisted data or external contracts are touched
5. Changelog, release notes, or public API docs if the feature affects externally visible behavior

If the workspace lacks formal docs, infer conventions from adjacent code, tests, config files, and scripts. If key assumptions still cannot be established safely after this research, stop and recommend `/do-research <topic>` instead of inventing them.

Do not skip this step. Plans written without reading the docs will be wrong.

---

## Step 2 — Decompose into tasks

Apply this strategy:

1. Identify the **end-state deliverables** — what files must exist, what checks must pass, what behaviour must work
2. Work **backward** from the deliverables to define tasks
3. Each task must be:
   - **Atomic** — completable in a single focused session (roughly 1–20 files touched)
   - **Verifiable** — has a concrete workspace-native verify command (focused test, typecheck, lint, build, script, etc.)
   - **Externally checkable** — prefer tasks that add or update automated tests or validations another engineer or CI can run independently, rather than relying only on local manual checking
   - **Dependency-aware** — explicitly lists which task IDs must be `[x]` before it can start
   - **File-annotated** — populate `Files to read` (relevant research memos, docs, contracts, existing source files, tests, or other references to consult) and `Files to modify` (files that will be created or changed) using the knowledge you gained in Step 1. Be specific and use full paths relative to the workspace root. This lets `/do-start` go straight to the right files without re-researching.
4. Assign IDs sequentially: T1, T2, T3 ...
5. Research tasks that remove critical uncertainty come first. Foundational tasks (data model, shared types/utilities, config) come next. Features built on them come after. Docs, changelog, and release-note work come last.

### Persisted data / schema rule

If the feature changes persisted data, schemas, contracts, or generated artifacts, the plan **must** include every workspace-required step as separate explicit tasks. Determine the exact workflow from workspace guidance and existing patterns before writing the plan.

Common examples include:

- updating schema or model definitions
- creating or updating migrations
- regenerating clients, types, or other generated artifacts
- updating docs, data dictionaries, ERDs, or API contracts if the workspace maintains them
- updating changelog or release notes if required
- adding or updating tests that cover the new data shape or external behavior
- running the workspace's generation and validation commands

If the workspace documents a mandatory workflow, treat every required step as non-negotiable even if it seems minor.

---

## Step 3 — Write the plan file

1. Slugify the feature description: lowercase, words separated by hyphens, no special characters, max 6 words. Example: "add BetterAuth session handling to api" → `betterauth-session-handling-api`
2. Read the template at `${CLAUDE_SKILL_DIR}/references/template.md`
3. Ensure the `plans/` directory exists, then write the plan to `plans/<slug>.md`, filling in every section.

   The plan file must begin with YAML front matter. Populate and keep these fields accurate from the start:

   - `title` — same value as the H1 heading
   - `slug` — the plan slug / filename stem
   - `description` — a short summary of the plan, max 70 tokens
   - `status` — `pending` for all new plans created by `/do-plan`
   - `task_count` — the total number of task blocks in the plan
   - `created_at` — the actual local date and time when the plan was generated, format `YYYY-MM-DD HH:MM`
   - `updated_at` — same value as `created_at` for a new plan, same format
   - `started_at` — `null` for a new plan
   - `completed_at` — `null` for a new plan

   The markdown body must mirror that metadata in `## Plan summary`. Keep the summary values aligned with the front matter:

   - `Description` mirrors `description`
   - `Status` mirrors `status`
   - `Slug` mirrors `slug`
   - `Task count` mirrors `task_count`
   - `Created` mirrors `created_at`
   - `Last updated` mirrors `updated_at`
   - `Started` mirrors `started_at` (`not started` when null)
   - `Completed` mirrors `completed_at` (`not completed` when null)

   Fill in the rest of the plan as follows:

   - **Related research** — bullet list of the research memo paths you actually used while planning, with a short reason for each. If none were used, write `_None linked._`
   - **Goal** — one sentence describing the observable end state
   - **Acceptance criteria** — bullet list of checkable conditions. Always include the primary observable outcome and the relevant workspace-native validation commands. Prefer automated tests or validations that can be re-run independently by another engineer or CI.
   - **Tasks** — one `### Tx — <title>` block per task with Status, Depends on, Verify, Files to read, Files to modify, and Notes
   - **Decisions log** — add one initial entry: `YYYY-MM-DD — Initial plan created for: <feature description>`
   - **Handoff notes** — leave as `_No handoff yet._`

Use today's actual local date and time for the front matter timestamps and use today's date for the decisions log entry.

4. Update `plans/INDEX.md`:
   - If `plans/` does not exist, create it first
   - Add a new row for this plan under the `pending` section (after any `in-progress` rows, before `done` and `abandoned` rows)
   - The row must mirror the plan's front matter using this format: `| \`pending\` | <title> | [<slug>](<slug>.md) | <description> | <task_count> | YYYY-MM-DD HH:MM | YYYY-MM-DD HH:MM | - | - |`
   - Use `-` in the `Started` and `Completed` columns when `started_at` or `completed_at` are `null`
   - Use today's actual date and current time for the `Created` and `Updated` columns on a new plan
   - If `plans/INDEX.md` does not exist, create it with this header and table:

```md
# Plans Index

<!-- Status key: pending | in-progress | done | abandoned -->
<!-- Ordered by: in-progress → pending → done → abandoned -->
<!-- Updated automatically by /do-plan, /do-start, /do-amend -->
<!-- Each row mirrors plan front matter: status, title, slug link, description, task_count, created_at, updated_at, started_at, completed_at -->
<!-- Use `-` for null started_at/completed_at values. -->

| Status | Title | Plan | Description | Tasks | Created | Updated | Started | Completed |
| ------ | ----- | ---- | ----------- | ----- | ------- | ------- | ------- | --------- |
```

    - Keep the rows ordered as `in-progress`, then `pending`, then `done`, then `abandoned`

`/do-plan` must create the YAML front matter, the mirrored `## Plan summary` section, the `## Related research` section, and `plans/INDEX.md`. `/do-start` and `/do-amend` rely on the plan structure being present and accurate.

---

## Step 4 — Report back

After writing the file, output:

```
Plan written to plans/<slug>.md

Related research:
  - <path or "none">

Goal: <goal sentence>

Tasks:
  T1 — <title> [verify: <command>]
  T2 — <title> [depends: T1] [verify: <command>]
  ...

To begin execution: /do-start plans/<slug>.md
```

Then ask: "Does this plan look right, or should I adjust anything before you run `/do-start`?"

Do not begin executing any tasks. Your job ends when the plan file is written and reviewed.
