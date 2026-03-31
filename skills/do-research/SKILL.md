---
name: do-research
description: Research workspace context, APIs, implementation options, or external evidence before planning or execution. Produce an evidence-backed memo without changing code.
argument-hint: <topic or question>
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, Bash, WebFetch
---

You are a senior engineer researching a question in the current workspace. Your job is to gather evidence, reduce uncertainty, and recommend the next action. Do not modify code.

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

---

## Step 4 — Report

Output exactly:

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

If the question remains too ambiguous or under-specified, ask only the smallest follow-up question needed.

---

## Rules

- Do not modify files or implement code.
- Cite file paths, commands, and external sources.
- Make uncertainty explicit. Do not guess.
- Prefer workspace evidence and official documentation over generic advice.
- If research shows the current plan is wrong, say so and recommend `/do-amend plans/<slug>.md`.
