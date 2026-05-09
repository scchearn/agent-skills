---
name: do-plan
description: Use when a workspace task, feature, or bugfix needs an execution-ready implementation plan with ordered, verifiable steps grounded in current repo context, relevant persisted research, spec files, and optional wiki notes before any code is written.
argument-hint: <feature description>
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, Write, Edit, Skill
---

You are a senior engineer working in the current workspace. Your job is to decompose a feature description into a rigorous, executable implementation plan, not to write any code yet.

## Non-negotiables

- Stay in planning mode. Do not implement code, edit source files outside plan artifacts, or claim the task is already done.
- Read current repo context before planning. Research memos, spec files, and wiki notes are accelerators, not authority over the current repo state.
- Produce a plan that a smaller tool-capable model could execute reliably: every task must be atomic, dependency-ordered, externally verifiable, and explicit about what to read and what to modify.
- Keep this skill authoritative for the output contract: always write `plans/<slug>.md` and keep `plans/INDEX.md` synchronized. Never switch to `docs/superpowers/plans/` or any other plan location.
- If critical uncertainty remains after local research, stop and recommend `/do-research <topic>` instead of inventing facts.

## Input

The feature description is: $ARGUMENTS

---

## Step 0 - Optional superpowers preflight

Before any repo exploration, check whether the harness can invoke other skills.

If the current conversation or harness context already indicates that `using-superpowers` is loaded, do not invoke it again. Treat it as already active and continue under that guidance.

Otherwise, if skill invocation is supported, you may do one safe availability check or one direct invocation attempt for `using-superpowers`.

- Treat that skill as process guidance only inside `/do-plan`.
- If it routes toward other planning skills with a different artifact format, keep any useful reasoning or decomposition guidance, but normalize the final output back to this skill's contract.
- `/do-plan` remains authoritative for `plans/<slug>.md`, the required markdown sections, YAML front matter, and `plans/INDEX.md` updates.

If skill invocation is unavailable, unsupported, the lookup or invocation errors, or `using-superpowers` is not installed, treat that as unavailable and continue immediately with this native `/do-plan` workflow. Do not retry, do not block, and do not ask the user a follow-up question just for skill availability.

---

## Step 1 — Load context

Before decomposing anything, inspect the workspace to understand its structure, conventions, and constraints. Start by checking for relevant persisted research under `plans/research/` and spec files under `specs/`, then read the current workspace state. Use search and file reads to discover the right files instead of assuming fixed paths.

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

### Spec files

If `specs/` exists:

1. List `specs/*.md`
2. Derive 3-8 search terms from the feature description
3. Read only the front matter or opening section of each spec for discovery. Do not read every spec file in full.
4. Shortlist likely specs using filename similarity and front matter fields like `title`, `description`, and `keywords`
5. Read only the opening section of the 1-3 best candidates first (front matter + top of file)
6. Read the full spec only if it is directly relevant to the feature you are planning
7. Treat spec files as accelerators, not source of truth. If a spec conflicts with the current repo state, trust the current repo state and note the mismatch in the plan's decisions log
8. If you use a spec, add its path to `## Related specs` in the plan and include it in relevant task `Files to read`

After that, read the most relevant current workspace material you can find, including:

1. Root guidance such as `AGENTS.md`, `CLAUDE.md`, `README.md`, `CONTRIBUTING.md`, or similar instructions
2. Relevant product, domain, or architecture docs under locations like `docs/`, `documentation/`, `specs/`, `adr/`, `design/`, or app/package-local docs
3. Existing source files, tests, configs, scripts, and examples near the feature area
4. Schema, model, migration, API contract, or generated-artifact files if persisted data or external contracts are touched
5. Changelog, release notes, or public API docs if the feature affects externally visible behavior

### Optional wiki context

If the workspace contains a wiki root with files such as `SCHEMA.md`, `index.md`, `log.md`, or a legacy root `overview.md`:

1. Read the schema and main hub notes first
2. Read only the wiki notes directly relevant to the feature area
3. Treat the wiki as an accelerator for durable workspace knowledge, not as the authority over the current repo state
4. If the wiki conflicts with current repo state, trust the current repo state and note the mismatch in the plan's decisions log if it materially affects planning

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
   - **File-annotated** — populate `Files to read` (relevant research memos, spec files, wiki notes, docs, contracts, existing source files, tests, or other references to consult) and `Files to modify` (files that will be created or changed) using the knowledge you gained in Step 1. Be specific and use full paths relative to the workspace root. This lets `/do-start` go straight to the right files without re-researching.
   - **Thorough enough for smaller models** — write enough detail that another model does not need to guess what success looks like, which files matter, or what command proves the task
   - **Topology-aware** (optional) — if a task clearly benefits from multi-agent execution, add an `Execution` field using the structured format described in Step 3.5. This is optional metadata, not a mandatory field, but if you include it, make it parseable enough for `/do-start` to launch safely.
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
2. Read the template at `${CLAUDE_SKILL_DIR}/references/template.md`. If that variable is unavailable in the current harness, read the same skill-local reference file via the harness-equivalent path to `references/template.md` instead.
3. Ensure the `plans/` directory exists, then write the plan to `plans/<slug>.md`, filling in every section.

   The plan file must begin with YAML front matter. Populate and keep these fields accurate from the start:

   - `title` — same value as the H1 heading
   - `slug` — the plan slug / filename stem
   - `description` — a short summary of the plan, max 70 tokens
   - `status` — `pending` for all new plans created by `/do-plan`
   - `task_count` — the total number of task blocks in the plan
   - `created_at` — the actual local date and time when the plan was generated, format `YYYY-MM-DD HH:MM`
   - `started_at` — `null` for a new plan
   - `completed_at` — `null` for a new plan

   YAML front matter is the only authoritative plan metadata. Do not duplicate that metadata elsewhere in the markdown body.

   Fill in the rest of the plan as follows:

   - `# <title>` — same value as `title`
    - **Related research** — bullet list of the research memo paths you actually used while planning, with a short reason for each. If none were used, write `_None linked._`
    - **Related specs** — bullet list of the spec file paths you actually used while planning, with a short reason for each. If none were used, write `_None linked._`
   - **Goal** — one sentence describing the observable end state
   - **Acceptance criteria** — bullet list of checkable conditions. Always include the primary observable outcome and the relevant workspace-native validation commands. Prefer automated tests or validations that can be re-run independently by another engineer or CI.
   - **Tasks** — one `### Tx — <title>` block per task with Status, Depends on, Verify, Files to read, Files to modify, and Notes
   - **Decisions log** — add one initial entry: `YYYY-MM-DD — Initial plan created for: <feature description>`
   - **Handoff notes** — leave as `_No handoff yet._`

Use today's actual local date and time for `created_at`, and use today's date for the decisions log entry.

4. Update `plans/INDEX.md`:
   - If `plans/` does not exist, create it first
   - If `plans/INDEX.md` already exists in the older wide format with timestamp columns, rewrite it to the slim schema before inserting or updating rows
   - Add a new row for this plan under the `pending` section (after any `in-progress` rows, before `done` and `abandoned` rows)
   - The row must mirror the plan's front matter using this format: `| \`pending\` | <title> | [<slug>](<slug>.md) | <description> | <task_count> |`
   - If `plans/INDEX.md` does not exist, create it with this header and table:

```md
# Plans Index

<!-- Status key: pending | in-progress | done | abandoned -->
<!-- Ordered by: in-progress → pending → done → abandoned -->
<!-- Updated automatically by /do-plan, /do-start, /do-amend -->
<!-- Each row mirrors plan front matter: status, title, slug link, description, task_count -->

| Status | Title | Plan | Description | Tasks |
| ------ | ----- | ---- | ----------- | ----- |
```

    - Keep the rows ordered as `in-progress`, then `pending`, then `done`, then `abandoned`

`/do-plan` must create the YAML front matter, the required markdown sections, and `plans/INDEX.md`. `/do-start` and `/do-amend` rely on the YAML metadata and section headers being present and accurate.

---

## Step 3.5 — Evaluate execution topology

After writing the plan, evaluate whether any tasks or contiguous task groups would benefit from multi-agent execution via hcom. This is a generous evaluation — always check every task.

### Trigger conditions

For each task, check whether any of these conditions apply:

| Trigger | Recommended topology |
|---|---|
| Risky or destructive implementation (data migrations, schema changes, force pushes) | `planner-executor-reviewer` |
| Needs independent code review before merge (auth changes, security-sensitive code, shared APIs) | `worker-reviewer` |
| Parallel branches that can run independently (independent features, separate modules) | `hub-spoke` |
| Large scope — roughly 5+ tasks in a cohesive group | `hub-spoke` or `sequential-cascade` depending on dependency structure |
| Cross-cutting concerns touching multiple domains (frontend + backend + infra) | `hub-spoke` |
| High-judgment or ambiguous decisions (design choices, architecture selection) | `ensemble-with-judge` |
| Staged refinement where later work depends on earlier output (planning → execution → review) | `sequential-cascade` |

### Default

If no trigger matches a task, it runs as a **single agent**. No annotation is needed.

### Annotating tasks

For tasks where a trigger matches, add an `Execution` field to the task block.

Prefer this structured form when you know the concrete agent details:

```
- **Execution:** topology: <topology> | agent: <agent-tag> | worktree: <path> | branch: <branch-name> | model: <provider/model> | rules: <behavioral rules>
```

If the plan only knows the topology and rationale, you may use an advisory-only form instead. `/do-start` will treat that as inline execution unless later plan edits add concrete launch fields:

```
- **Execution:** topology: worker-reviewer | rules: independent review required because T3 through T5 all modify authentication
```

For following tasks that reuse the same agent, prefer a short continuation form:

```
- **Execution:** same agent as T3
```

### Scope

This step produces execution metadata, not runnable automation. Do not generate shell scripts, agent TOML files, or launch commands here. The `Execution` field should tell `/do-start` enough to choose between inline hub execution and hcom delegation. If the plan lacks concrete launch fields, that is acceptable, but it means `/do-start` will fall back to inline execution for that session.

If no tasks match any trigger, skip this step entirely — no `Execution` fields are added and no topology section appears in the report-back.

---

## Step 4 — Optional wiki write-back

If a wiki exists, decide whether planning surfaced durable knowledge worth preserving there.

Good candidates include:

- stable architecture constraints
- durable workflow or generation requirements
- reusable domain distinctions
- clarified system boundaries or invariants

Do not write back:

- the task list itself
- ephemeral sequencing choices
- narrow task-management chatter

If the finding is durable:

1. Prefer updating an existing relevant topic, concept, entity, or analysis note
2. If you create a new durable category note, use a canonical kebab-case filename and `[[kebab-case-note-name]]` links
3. Update `index.md` if durable pages changed
4. Append `log.md` with a parseable heading like:

```md
## [YYYY-MM-DD] plan | <feature>
```

If no wiki exists or nothing rises above planning noise, do nothing beyond the plan file.

---

## Step 5 — Report back

After writing the file, output:

```
Plan written to plans/<slug>.md

Related research:
  - <path or "none">

Related specs:
  - <path or "none">

Filed back into wiki:
  - <path or "none">

Goal: <goal sentence>

Tasks:
  T1 — <title> [verify: <command>]
  T2 — <title> [depends: T1] [verify: <command>]
  ...

Execution hints:
  - T3-T5: worker-reviewer (authentication changes need independent review)
  - All other tasks: single agent

To begin execution: /do-start plans/<slug>.md
```

Then ask: "Does this plan look right, or should I adjust anything before you run `/do-start`?"

Do not begin executing any tasks. Your job ends when the plan file is written and reviewed.
