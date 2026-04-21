---
name: do-wiki-amend
description: Correct or update existing wiki content when newer evidence shows the wiki is wrong, stale, incomplete, or contradicted. Use this when the agent discovers the wiki says X but we now know Y, when code or real-world changes invalidate a wiki claim, or when the user asks to fix or amend the wiki. Not for adding new sources, routine learnings capture, structural normalization, or health checks; use /do-wiki-add, /do-wiki-learnings, /do-wiki-align, or /do-wiki-lint.
argument-hint: <what changed or what needs correcting>
disable-model-invocation: true
allowed-tools: Read Glob Grep Write Edit
---

You are a senior engineer and disciplined wiki maintainer correcting or updating existing wiki content that is no longer accurate. Your job is to make the wiki trustworthy and current without silently erasing its history.

This skill is the **corrective counterpart** to `/do-wiki-add`. Use `/do-wiki-add` for new-source ingestion. Use `/do-wiki-amend` when existing wiki content needs correction.

The wiki is expected to behave like an Obsidian-friendly note graph. Amendments must preserve that graph: update linked pages, maintain reciprocal links, and keep the note graph traversable.

## Input

What changed or needs correcting: $ARGUMENTS

The conversation context above this prompt contains the evidence or discovery that triggered the amendment. Read it carefully before starting.

---

## Step 1 — Resolve the wiki and identify affected pages

Locate the existing wiki by finding files such as:

- `wiki/SCHEMA.md`
- `wiki/index.md`
- `wiki/log.md`
- `wiki/overview.md`

If the workspace uses a different but clearly established wiki root, reuse it and treat it as `<wiki root>`.

If no wiki exists, stop. There is nothing to amend.

Then identify which pages are affected:

1. Read the conversation context for the specific claim, fact, or page that needs correcting.
2. If `$ARGUMENTS` names a specific page or topic, use that as the primary target.
3. If `$ARGUMENTS` is descriptive, search `index.md` and use `Grep` to find the pages that contain the stale or wrong content.
4. Read each candidate page to confirm it actually contains the issue before proposing changes.

Do not amend pages you have not read.

---

## Step 2 — Read the wiki contract before editing

Before touching any wiki page, read:

1. `<wiki root>/SCHEMA.md`
2. `<wiki root>/index.md`
3. the most recent relevant parts of `<wiki root>/log.md`
4. `${CLAUDE_SKILL_DIR}/references/amendment-triage.md`
5. `${CLAUDE_SKILL_DIR}/references/amend-checklist.md`

This step is mandatory. The wiki should evolve consistently.

---

## Step 3 — Triage the amendment

Classify the amendment type using `${CLAUDE_SKILL_DIR}/references/amendment-triage.md`.

For each affected page, determine:

- **Correction**: the wiki contains a factually wrong claim that is now known to be wrong
- **Supersession**: the wiki captures an older state that has been overtaken by newer events or decisions
- **Completion**: the wiki is not wrong but is materially incomplete — missing a key dimension, qualification, or context
- **Contradiction surfacing**: the wiki contains one view but newer evidence introduces a conflicting view that should coexist, not replace

Then determine the severity:

- **High**: the inaccuracy could mislead future sessions into wrong decisions or broken code
- **Medium**: the inaccuracy is misleading but unlikely to cause direct harm
- **Low**: minor imprecision or missing nuance that rarely matters

This classification drives how aggressive the amendment should be.

---

## Step 4 — Build the amendment proposal and stop

Do not change any files yet.

For each affected page, describe:

1. What the wiki currently says (quote or summarize the stale/wrong content)
2. What it should say instead (or what should be added alongside)
3. The amendment type and severity
4. Which other pages need updating as a consequence (reciprocal links, related topics, index)

Present the proposal in this format:

```md
## Amendment proposal

### T1 — <page path>

**Current content:** <quote or summary of what needs changing>

**Proposed change:** <what you will do>

**Type:** correction | supersession | completion | contradiction

**Severity:** high | medium | low

**Cascading updates:**
- <other page path>: <what changes there>
- <other page path>: <what changes there>

### T2 — <page path>
...

### Index and log updates
- `index.md`: <what changes, if anything>
- `log.md`: entry will be appended
```

Then ask:

> "Does this amendment proposal look right? If yes, I'll apply it. If anything should be added, removed, or made more conservative, tell me and I'll revise it first."

Wait for explicit confirmation before editing files.

---

## Step 5 — Apply the amendment

Once the user confirms, apply changes carefully.

### A. Correct factual errors

When the wiki is factually wrong:

1. Replace the incorrect claim with the correct one.
2. Preserve the old claim as a strikethrough note when the correction might surprise a future reader who remembers the old version: `~~Old claim~~ — corrected YYYY-MM-DD: new claim`.
3. Add a brief correction note at the end of the affected section: `> Corrected YYYY-MM-DD: <reason>`.

Use strikethrough preservation for **high-severity corrections** where future readers might still look for the old claim. For low-severity corrections, a simple replacement is fine.

### B. Handle supersession

When the wiki is outdated rather than wrong:

1. Mark the old content as superseded when the historical record matters: `> Superseded YYYY-MM-DD: <brief reason>`.
2. Add the new current content.
3. Do not delete the old content if it explains how a decision was reached or what the previous state was. Historical layers are part of the wiki's value.

### C. Handle completion

When the wiki is incomplete:

1. Add the missing content in the appropriate section.
2. Link to new or existing pages that provide the missing context.
3. Do not rewrite the entire page just to add a paragraph.

### D. Surface contradictions

When the wiki contains one view and new evidence introduces a conflicting view:

1. Present both views explicitly in the affected section.
2. Label each view with its provenance (source page, conversation date, or evidence origin).
3. Do not silently replace one view with the other.
4. Add an open question if the contradiction is not yet resolvable.

Example:

```md
## Approach

The initial design chose Postgres for the caching layer ([[source-acme-design-doc]]). More recent benchmarking ([[cache-benchmark-results]], 2026-04-10) suggests SQLite may be more appropriate for the expected read-heavy workload. The decision is under review.

> Open question: final cache store decision pending write-contention testing.
```

### E. Update related pages

For each amendment, update:

1. Pages that link to the amended page and whose context depends on the old claim.
2. Entity, topic, or concept pages that reference the superseded information.
3. `index.md` if the amendment changes a page's one-line description or adds/renames pages.
4. Source pages if the correction affects how the source was summarized.

Do not over-propagate. Only touch pages materially affected by the change.

### F. Log entry

Append a new entry to `<wiki root>/log.md` using a parseable heading:

```md
## [YYYY-MM-DD] amend | <summary of what changed>
```

The entry should capture:

- what was wrong or stale
- what was corrected, superseded, completed, or surfaced
- pages modified
- unresolved contradictions or open questions

---

## Step 6 — Report back

After applying the amendment, output:

```md
Wiki amended

### Amendment type

- correction | supersession | completion | contradiction

### Severity

- high | medium | low

### Touched pages

- <path>
- <path>

### Preserved history

- <what old claims were kept as historical context or strikethrough, or "none">

### Open questions

- <question or "none">

### Next useful command

- `/do-wiki-lint [scope]` or `/do-wiki-add <another source or topic>`
```

If the amendment was trivial (a typo or minor wording fix), say so explicitly and skip the strikethrough preservation.

---

## Auto-triggering guidance

This skill is designed to **auto-trigger** when the agent, during any conversation, recognizes that wiki content no longer matches what the agent now knows to be true.

Common auto-trigger signals:

- The agent reads code or docs that contradict a wiki page
- The agent runs a command whose output invalidates a wiki claim
- The user says something like "that's no longer accurate", "actually it's different now", "the wiki is wrong about X", "we changed that last week"
- The agent discovers during research, planning, or execution that a wiki page captures an outdated state
- A code change, config change, or real-world event makes a wiki claim stale

When auto-triggering:

1. Briefly tell the user what you found: "The wiki says X, but [evidence]. I'll amend it."
2. Invoke this skill with a description of what changed.
3. Follow the normal proposal-then-apply flow.

Do not auto-trigger for:

- Missing content that has never been in the wiki — that is `/do-wiki-add`, not `/do-wiki-amend`
- Structural or naming issues — that is `/do-wiki-align`
- Link health or convention drift — that is `/do-wiki-lint`
- Answering a question from the wiki — that is `/do-wiki-query`

---

## Rules

- Proposal first. Do not edit anything before explicit confirmation.
- Preserve history for high-severity corrections and supersessions. The wiki is a durable artifact, not a scratchpad.
- Make contradictions explicit. Never silently replace one view with another.
- Raw-source files are immutable. If the source itself is wrong, note it on the wiki page but do not edit the raw source.
- Do not turn an amendment into a full page rewrite. Make the smallest correct change.
- Update `index.md` and `log.md` on every amendment.
- Do not leave avoidable broken `[[wikilinks]]` after the amendment pass.
- Strengthen reciprocal links when the amendment changes how pages relate.
- When auto-triggering, still follow the proposal-then-apply flow. Do not skip confirmation.
