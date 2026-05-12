# Shared reference — optional wiki context

This snippet is referenced by `do-research`, `do-plan`, `do-start`, `do-amend`, and the wiki skills. It defines how to use a workspace wiki as an *accelerator* without ever treating it as the authority over current repo state.

## When this applies

The workspace contains a wiki root — a directory with files such as `SCHEMA.md`, `index.md`, `log.md`, or a legacy root `overview.md`. If none of those files exist, there is no wiki and this snippet does not apply.

## How to read the wiki

1. Read the schema and the main hub notes (`SCHEMA.md`, `index.md`) first.
2. Read any wiki notes that are already named in the task's `Files to read` (or in the prompt's stated scope).
3. If the task does not name wiki notes, search for notes directly relevant to the immediate work using qmd (if ready) or Grep/Glob.
4. Treat the wiki as an *accelerator* for durable workspace knowledge — a way to skip re-deriving facts that have been written down before. It is not a source of truth.

## Trust order when wiki and code disagree

1. Current repo state (source files, tests, configs, generated artifacts) — authoritative.
2. Workspace guidance files (`AGENTS.md`, `CLAUDE.md`, `README.md`, `CONTRIBUTING.md`) — authoritative.
3. Research memos under `plans/research/` — recent and explicit.
4. Wiki notes — durable but may be stale.

If the wiki conflicts with current repo state, trust the repo. Only update the wiki later if the correction is durable (an architecture fact, a workflow change, a domain rule), not ephemeral task chatter.

## What not to do

- Do not read the entire wiki up front. Scope the read to the relevant notes.
- Do not write back to the wiki from inside this snippet — see `wiki-write-back.md` for that.
- Do not invent a wiki root. If none of the marker files exist, skip wiki context entirely.
