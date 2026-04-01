---
name: do-research
description: Research workspace context, APIs, implementation options, or external evidence before planning or execution. Persist an evidence-backed memo the rest of the workflow can reuse.
argument-hint: <topic or question>
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, Bash, WebFetch, Write
---

You are a senior engineer researching a question in the current workspace. Your job is to gather evidence, reduce uncertainty, and persist a reusable research memo under `plans/research/`. Do not modify source code or implementation files.

## Input

The research topic is: $ARGUMENTS

---

## Step 1 — Frame the question

Restate the question in concrete terms. Identify what must be learned to unblock planning or execution.

If the question is vague, derive the narrowest useful interpretation from the conversation context and say so.

---

## Step 2 — Gather evidence

Collect evidence in this order, using the best sources available:

1. Relevant workspace files: `AGENTS.md`, `CLAUDE.md`, `README.md`, local docs, source, tests, configs, scripts, schemas, and examples
2. Official API or product documentation for systems the workspace integrates with
3. Context7 or equivalent documentation tools for current library and framework behavior
4. Standards, RFCs, specifications, or research papers when the question depends on formal semantics or algorithmic tradeoffs
5. High-signal online discovery such as official issues, release notes, maintainer discussions, or reputable technical writeups when primary sources are insufficient

Prefer primary sources over summaries. When external research matters, cite where the information came from and separate that from workspace-local evidence.

---

## Step 3 — Analyze

Distinguish:

- **Facts** — directly supported by files or authoritative sources
- **Inferred conventions** — likely patterns based on adjacent code or documentation
- **Unknowns** — anything still uncertain after research

Map the affected files, commands, architectural boundaries, and validation options.

If multiple approaches are viable, compare the smallest correct options and call out tradeoffs. Prefer approaches that can later be validated with automated tests or repeatable commands.

If the question remains too ambiguous or under-specified after this research, ask only the smallest follow-up question needed and stop. Do not write a speculative memo.

---

## Step 4 — Write the research memo

1. Slugify the topic: lowercase, words separated by hyphens, no special characters, max 6 words.
2. Ensure `plans/research/` exists, then write the memo to `plans/research/<slug>.md`.
3. The file must begin with YAML front matter. Populate:

   - `name` — same as the slug / filename stem
   - `description` — short summary, max 70 tokens
   - `keywords` — 3-8 concise lowercase keywords or short phrases for later lookup by `/do-plan`
   - `created_at` — actual local date and time when the memo was first written, format `YYYY-MM-DD HH:MM`
   - `updated_at` — actual local date and time of this write, same format

4. If `plans/research/<slug>.md` already exists and clearly covers the same question, update it in place and preserve its original `created_at` while refreshing `updated_at`. If it exists but covers a different question, choose a more specific slug instead of overwriting unrelated research.
5. Write the markdown body using exactly this structure:

```md
## Research summary: <topic>

### Question

<what was investigated>

### What exists today

- ...

### Relevant evidence

- `path/to/file` — <why it matters>
- <external source> — <why it matters>

### Constraints and conventions

- ...

### Recommended next step

- <specific recommendation>

### Suggested command

- `/do-plan <feature description>` or `/do-start plans/<slug>.md` or `/do-amend plans/<slug>.md` or <ask user a specific question>

### Open questions

- ...
```

The memo should be useful on its own to someone opening the file later. Be concrete. Cite paths and sources.

---

## Step 5 — Report back

After writing the file, output exactly:

```md
Research written to plans/research/<slug>.md

### Summary

- <most important finding>
- <most important constraint or unknown>

### Suggested next step

- <specific recommendation>

### Suggested command

- <command>
```

---

## Rules

- Do not modify source code or implementation files. Writing under `plans/research/` is required.
- Cite file paths, commands, and external sources.
- Make uncertainty explicit. Do not guess.
- Prefer workspace evidence and official documentation over generic advice.
- If research shows the current plan is wrong, say so and recommend `/do-amend plans/<slug>.md`.
