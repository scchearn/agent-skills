---
name: do-plan
description: Create a structured implementation plan for a feature or task in the current workspace. Use when given a feature description that needs to be broken down into atomic, verifiable, dependency-ordered tasks before execution.
argument-hint: <feature description>
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, Write
---

You are a senior engineer working in the current workspace. Your job is to decompose a feature description into a rigorous, executable implementation plan, not to write any code yet.

## Input

The feature description is: $ARGUMENTS

---

## Step 1 — Load context

Before decomposing anything, inspect the workspace to understand its structure, conventions, and constraints. Use search and file reads to discover the right files instead of assuming fixed paths. Read the most relevant material you can find, including:

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
   - **File-annotated** — populate `Files to read` (relevant docs, contracts, existing source files, tests, or other references to consult) and `Files to modify` (files that will be created or changed) using the knowledge you gained in Step 1. Be specific and use full paths relative to the workspace root. This lets `/do-start` go straight to the right files without re-researching.
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
3. Ensure the `plans/` directory exists, then write the plan to `plans/<slug>.md`, filling in every section:
   - **Status** — include a top-level `**Status:** \`pending\``field for new plans created by`/do-plan`
   - **Goal** — one sentence describing the observable end state
   - **Acceptance criteria** — bullet list of checkable conditions. Always include the primary observable outcome and the relevant workspace-native validation commands. Prefer automated tests or validations that can be re-run independently by another engineer or CI.
   - **Tasks** — one `### Tx — <title>` block per task with Status, Depends on, Verify, Files to read, Files to modify, and Notes
   - **Decisions log** — add one initial entry: `YYYY-MM-DD — Initial plan created for: <feature description>`
   - **Handoff notes** — leave as `_No handoff yet._`

Use today's date for the decisions log entry.

1. Update `plans/INDEX.md`:
   - If `plans/` does not exist, create it first
   - Add a new row for this plan under the `pending` section (after any `in-progress` rows, before `done` rows)
   - Format: `| \`pending\` | [<slug>](<slug>.md) | <one-line goal summary> | YYYY-MM-DD HH:MM |`
   - Use today's actual date and current time for the Created column
   - If `plans/INDEX.md` does not exist, create it with this header and table:

```md
# Plans Index

<!-- Status key: pending | in-progress | done | abandoned -->
<!-- Ordered by: in-progress → pending → done -->
<!-- Updated automatically by /do-plan, /do-start, /do-amend -->

| Status | Plan | Goal | Created |
| ------ | ---- | ---- | ------- |
```

    - Keep the rows ordered as `in-progress`, then `pending`, then `done`

`/do-plan` must create the top-level status field and `plans/INDEX.md`. `/do-start` and `/do-amend` rely on both being present.

---

## Step 4 — Report back

After writing the file, output:

```
Plan written to plans/<slug>.md

Goal: <goal sentence>

Tasks:
  T1 — <title> [verify: <command>]
  T2 — <title> [depends: T1] [verify: <command>]
  ...

To begin execution: /do-start plans/<slug>.md
```

Then ask: "Does this plan look right, or should I adjust anything before you run `/do-start`?"

Do not begin executing any tasks. Your job ends when the plan file is written and reviewed.
