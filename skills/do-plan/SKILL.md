---
name: do-plan
description: Create a structured implementation plan for a feature or task in the Kora monorepo. Use when given a feature description that needs to be broken down into atomic, verifiable, dependency-ordered tasks before execution.
argument-hint: <feature description>
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, Write
---

You are a senior engineer on the Kora AMS monorepo. Your job is to decompose a feature description into a rigorous, executable implementation plan — not to write any code yet.

## Input

The feature description is: $ARGUMENTS

---

## Step 1 — Load context

Before decomposing anything, read the following to understand the codebase and constraints:

1. `AGENTS.md` or `CLAUDE.md` at the repo root — monorepo structure, architectural decisions, schema change workflow, and the 6-step change checklist
2. `apps/docs/docs/specifications/feature-list.md` — per-domain feature specs
3. `apps/docs/docs/specifications/data-dictionary.md` — field-level entity definitions
4. Any domain docs in `apps/docs/docs/domains/` that are relevant to the feature description (infer from domain names: identity, people, sessions, medical, wellness, loads, questionnaires, calendar, periodization, matches, availability, communication, integrations, analytics)
5. Any ERDs in `apps/docs/layers/` for the relevant domains
6. Existing schema files in `packages/db/src/schema/` for any domain being touched

Do not skip this step. Plans written without reading the docs will be wrong.

---

## Step 2 — Decompose into tasks

Apply this strategy:

1. Identify the **end-state deliverables** — what files must exist, what checks must pass, what behaviour must work
2. Work **backward** from the deliverables to define tasks
3. Each task must be:
   - **Atomic** — completable in a single focused session (roughly 1–20 files touched)
   - **Verifiable** — has a concrete verify command (`pnpm check`, `pnpm build`, a specific test, etc.)
   - **Dependency-aware** — explicitly lists which task IDs must be `[x]` before it can start
   - **File-annotated** — populate `Files to read` (relevant docs, ERDs, existing source files to consult) and `Files to modify` (files that will be created or changed) using the knowledge you gained in Step 1. Be specific — use full paths relative to the repo root. This lets `/do-start` go straight to the right files without re-researching.
4. Assign IDs sequentially: T1, T2, T3 ...
5. Foundational tasks (schema, types, shared utilities) come first. Features built on them come next. Docs/changelog last.

### Schema change rule

If the feature touches the database schema, the plan **must** include all 6 steps from the AGENTS.md schema change workflow as separate tasks:

- T_schema — Update `packages/db/src/schema/<domain>.ts`
- T_types — Update inferred type exports in `packages/db/src/index.ts`
- T_erd — Update the ERD in `apps/docs/layers/<domain>.mmd`
- T_dict — Update `apps/docs/docs/specifications/data-dictionary.md`
- T_changelog — Add entry to `apps/docs/docs/specifications/changelog.md`
- T_migrate — Run `pnpm db:generate` from `packages/db/`

These must appear in the plan even if they seem minor. They are non-negotiable per the project conventions.

---

## Step 3 — Write the plan file

1. Slugify the feature description: lowercase, words separated by hyphens, no special characters, max 6 words. Example: "add BetterAuth session handling to api" → `betterauth-session-handling-api`
2. Read the template at `${CLAUDE_SKILL_DIR}/template.md`
3. Write the plan to `plans/<slug>.md`, filling in every section:
   - **Goal** — one sentence describing the observable end state
   - **Acceptance criteria** — bullet list of checkable conditions (always include `pnpm check` passes and `pnpm build` exits 0)
   - **Tasks** — one `### Tx — <title>` block per task with Status, Depends on, Verify, and Notes
   - **Decisions log** — add one initial entry: `YYYY-MM-DD — Initial plan created for: <feature description>`
   - **Handoff notes** — leave as `_No handoff yet._`

Use today's date for the decisions log entry.

4. Update `plans/INDEX.md`:
   - Add a new row for this plan under the `pending` section (after any `in-progress` rows, before `done` rows)
   - Format: `| \`pending\` | [<slug>](<slug>.md) | <one-line goal summary> | YYYY-MM-DD HH:MM |`
   - Use today's actual date and current time for the Created column
   - If `plans/INDEX.md` does not exist, create it with the standard header and table (including a `Created` column header)

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
