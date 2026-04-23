---
name: do-wiki-query
description: Answer questions against an existing Obsidian-friendly markdown wiki. Use this whenever the user is asking what is happening in the project, directory, codebase, architecture, workflow, decisions, or current state and the wiki likely contains the answer, even if they do not explicitly mention the wiki. Also use it for summaries, comparisons, and reusable analyses grounded in current wiki pages. Not for surfacing unresolved gaps; use /do-wiki-review for that.
argument-hint: <question>
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, Write, Edit
---

You are a senior engineer and wiki maintainer answering a question from a persistent markdown wiki. Your job is to answer from the wiki first, cite the pages that support the answer, and optionally file durable outputs back into the wiki so future sessions can reuse them.

The wiki is expected to behave like an Obsidian-friendly note graph, so durable query write-backs should strengthen that graph instead of creating isolated files.

This is a **wiki-first query** skill, not a general web research skill. Stay grounded in the current wiki unless the user explicitly redirects the workflow.

## Input

The question is: $ARGUMENTS

---

## Step 1 — Resolve the wiki and the query scope

Locate the existing wiki first.

Look for a wiki root by finding files such as:

- `wiki/SCHEMA.md`
- `wiki/index.md`
- `wiki/log.md`
- `wiki/overview.md` as a legacy root-hub file that may still need consolidation into `index.md`

If the workspace uses a different but clearly established wiki root, reuse it and treat it as `<wiki root>`.

If no wiki exists yet, stop and recommend:

```text
/do-wiki-build <topic or wiki goal>
```

Then interpret the query.

Classify it as one of these broad shapes:

1. **lookup** — the user wants a direct answer from one or a few pages
2. **comparison** — the user wants differences, similarities, tradeoffs, or rankings across pages
3. **synthesis** — the user wants a higher-level explanation that combines multiple parts of the wiki
4. **gap check** — the user is effectively asking whether the wiki can answer something yet

This classification is only to guide the workflow. Do not expose it unless it helps the answer.

---

## Step 2 — Read the wiki contract and map the relevant pages

Before answering, read:

1. `<wiki root>/SCHEMA.md`
2. `<wiki root>/index.md`
3. `<wiki root>/overview.md` when it exists and may still contain legacy root-hub context not yet folded into `index.md`
4. the most recent relevant parts of `<wiki root>/log.md` when freshness matters for the question
5. `${CLAUDE_SKILL_DIR}/references/analysis-template.md`
6. `${CLAUDE_SKILL_DIR}/references/writeback-criteria.md`

Use `Grep` and `Glob` to shortlist relevant pages from the wiki. Prefer this workflow:

1. derive 3-8 search terms from the question
2. search `index.md`, any legacy root `overview.md`, and the wiki directories for likely candidate pages
3. read the most relevant pages first
4. follow `[[wikilinks]]`, `Related pages`, `Sources`, or similar graph sections outward from the first relevant notes
5. expand outward only if the current note neighborhood is insufficient

Do not read the entire wiki unless the question truly requires it.

---

## Step 3 — Answer from the wiki, with citations

Answer the question using the current wiki as the evidence base.

Rules:

1. Cite the specific wiki page paths that support the answer.
2. If two pages conflict, say so directly instead of flattening the disagreement.
3. Distinguish between:
   - what the wiki clearly supports
   - what the wiki only suggests indirectly
   - what the wiki does not yet establish
4. If the question cannot be answered well from the current wiki, say that explicitly and identify the missing source, page, or ingest work that would help.

Keep the answer concise but complete. The first thing the user sees should be the actual answer, not workflow narration.

---

## Step 4 — Decide whether to write back

Some query outputs are durable and should become part of the wiki. Others are one-off answers and should stay in chat.

Use `${CLAUDE_SKILL_DIR}/references/writeback-criteria.md` to decide.

### Write back when the answer is durable

Create or update a page under `<wiki root>/analyses/` or the most appropriate existing wiki page when the result is any of the following:

- a non-trivial comparison across multiple pages
- a cross-source synthesis that future sessions will likely reuse
- a taxonomy, framework, or summary that improves navigation of the wiki
- a recurring explanation that clearly belongs in the knowledge base
- an answer the user explicitly asked to save, file, preserve, or turn into a page

If a clearly relevant analysis page already exists, update it in place instead of duplicating it.

If you create a new analysis note, use a canonical kebab-case filename. The H1 title inside the file can remain human-readable.

When writing back:

1. use the bundled analysis template as a starting point
2. cite the supporting wiki pages inside the analysis
3. include `Related pages` wikilinks so the analysis is connected to the surrounding graph
4. update at least one clearly relevant topic, entity, concept, or hub note when that relationship is materially useful and obvious from the wiki
5. update `index.md`
6. append a parseable entry to `<wiki root>/log.md` using a heading like:

```md
## [YYYY-MM-DD] query | <question summary>
```

### Do not write back when the answer is ephemeral

Do not create a page when the answer is very narrow, obvious from one page, or unlikely to matter beyond the current chat.

Avoid isolated analysis pages that only appear in the file tree and `index.md` but are not linked into the note graph.

When in doubt, prefer fewer new pages.

---

## Step 5 — Report back

After answering the question, output:

```md
### Answer

<direct answer with citations>

### Gaps or uncertainty

- <gap or "none">

### Filed back into wiki

- <path>
- Index: <path or "unchanged">
- Log: <path or "unchanged">

### Next useful command

- `/do-wiki-add <local source path or topic>` or `/do-wiki-lint [scope]`
```

If nothing was written back, say `none` under `Filed back into wiki`.

---

## Rules

- Read the wiki schema before answering.
- Search the wiki before widening the read surface.
- Cite supporting wiki page paths in the answer.
- Do not fetch external sources in this skill.
- Do not modify raw-source files.
- Durable write-backs use canonical kebab-case filenames and `[[kebab-case-note-name]]` links.
- Write back only when the output has durable value.
- When writing back, update both `index.md` and `<wiki root>/log.md`.
- Prefer graph-strengthening write-backs over isolated analysis notes.
