---
name: do-wiki-add
description: Read a local source file or synthesize from conversation context and integrate it into an existing Obsidian-friendly markdown wiki. Use this when the user wants to "add a source to the wiki", "add this document to the wiki", "save chat context to the wiki", "add this conversation to the wiki", or update the wiki from a local note, transcript, article, report, PDF, or the current discussion.
argument-hint: <local source path | topic or summary from chat>
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, Write, Edit
---

You are a senior engineer and disciplined wiki maintainer working in the current workspace. Your job is to compile content into the persistent wiki so future sessions inherit the knowledge instead of rediscovering it.

The target output is an Obsidian-friendly note graph:

- canonical kebab-case filenames for durable category notes
- internal links written as `[[kebab-case-note-name]]`
- small linked source, topic, entity, concept, and analysis notes
- reciprocal backlinks where the relationship is materially useful

This skill supports two modes: **file mode** (local file ingestion) and **chat-context mode** (synthesize from the current conversation). Do not fetch URLs directly in this skill.

## Input

The content to add is: $ARGUMENTS

---

## Step 1 — Resolve the source and the wiki

Start by locating both the source and the target wiki.

### Source resolution

Use the argument plus the current workspace to identify the source file or a very small intended batch of related files.

Rules:

1. Prefer an exact local path when the user provided one.
2. If the user gave a title or description instead of a path, search for the best candidate under likely source locations such as `raw/`, `sources/`, `research/`, `docs/`, `notes/`, or similar directories.
3. If more than one candidate is plausible, ask the smallest follow-up question needed.
4. If the user clearly requested a large batch, stop and ask them to narrow it or to run repeated adds. This skill is optimized for one source at a time.

### Wiki resolution

Find the existing wiki by looking for files such as:

- `wiki/SCHEMA.md`
- `wiki/index.md`
- `wiki/log.md`
- `wiki/overview.md`

If the workspace uses a different but clearly established wiki root, reuse it.

If no existing wiki can be found, stop and recommend:

```text
/do-wiki-build <topic or wiki goal>
```

---

## Step 1.5 — Detect mode

Determine whether this run operates in **file mode** or **chat-context mode**.

### Mode detection

1. If `$ARGUMENTS` resolves to an existing local file (or is clearly a file path with an extension), use **file mode**.
2. If `$ARGUMENTS` does not resolve to a file and instead looks like a topic, question, or natural-language summary, use **chat-context mode**.
3. If ambiguous (could be either a file description or a chat topic), ask the user: "Did you mean a local file path, or should I synthesize from our conversation?"

### Chat-context mode specifics

When running in chat-context mode:

1. The "source" is the current conversation context — facts, decisions, technical findings, and open questions discussed so far.
2. Read `${CLAUDE_SKILL_DIR}/references/chat-context-ingest.md` before proceeding to Step 2.
3. Use `$ARGUMENTS` as the topic or summary title. Slugify it for the source page filename.
4. On the source page, set `Source type: conversation` and record `Conversation date: YYYY-MM-DD`.
5. Conversation-sourced content is inherently less verified than a curated file. Err toward explicit uncertainty. Mark claims as "discussed" or "agreed in conversation" rather than "confirmed."
6. In the log entry, use the heading format `## [YYYY-MM-DD] add (chat) | <topic>` instead of `add (file)`.

File mode proceeds exactly as before — no behavioral changes.

---

## Step 2 — Read the wiki contract before editing

Before touching any wiki page, read:

1. the schema file for the wiki
2. `index.md`
3. the most recent relevant parts of `log.md`
4. any existing source page or topic/entity/concept pages that look directly related to the source
5. `${CLAUDE_SKILL_DIR}/references/source-summary-template.md`
6. `${CLAUDE_SKILL_DIR}/references/ingest-checklist.md`

This step is mandatory. The wiki should evolve consistently rather than page-by-page improvisation.

---

## Step 3 — Read the source and extract what matters

### File mode

Read the source file carefully.

Distinguish:

- **facts** directly supported by the source
- **claims or interpretations** made by the source
- **open questions** the source raises but does not settle
- **entities, concepts, and topics** that should be linked into the wiki

When the source is large, read enough of it to produce a faithful summary and then use targeted follow-up reads for the most relevant sections.

If the file format is not readable with the available tools, stop and say exactly what prevented ingestion.

### Chat-context mode

Synthesize the current conversation context into a structured summary.

Distinguish:

- **decisions and facts** agreed upon in conversation
- **claims or interpretations** raised but not fully settled
- **open questions** that remain unresolved
- **entities, concepts, and topics** that should be linked into the wiki

Err toward uncertainty. If a point was mentioned but not confirmed, flag it explicitly. Prefer "X was discussed as a likely approach" over "X is the approach."

---

## Step 4 — Update the wiki incrementally

Treat the wiki as a compiled artifact that must stay internally coherent.

### A. Source page

Create or update a source summary page under `wiki/sources/` or the workspace's equivalent source-page directory.

Use the bundled template as a starting point and adapt it to the schema already in use.

The source note should use a canonical kebab-case filename. The H1 title inside the note can remain human-readable.

Every durable `[[wikilink]]` on the source page should resolve to an existing canonical note or to a note you create or update during the same add pass.

If a page for this source already exists:

- update it in place
- preserve useful prior links or notes
- do not duplicate it under a second filename

### B. Related pages

Update the most relevant topic, entity, concept, or analysis pages.

Graph fan-out is required, not optional. Each add should leave behind a navigable local graph around the source, not just a standalone source summary.

Use these rules:

1. Only touch pages materially affected by the source.
2. If an important entity, concept, or topic is central to the source and still lacks a dedicated page, create a minimal canonical note for it using a kebab-case filename.
3. Prefer small linked updates over copying the same summary text into many pages.
4. If the new source contradicts an existing page, make the conflict explicit instead of silently replacing the older view.
5. If the new source supersedes an old claim, say so clearly and point to both sources.
6. When the source page links to a topic, entity, concept, or analysis note, update that note to link back under `Sources`, `Mentioned in`, or `Related pages` when the relationship is materially useful.
7. Avoid isolated durable notes. Every new note should be reachable from `index.md`, `overview.md`, or another durable note, and should contain at least one meaningful outbound link.

When you create a new topic, entity, or concept note during the add, keep it small but structured. A good minimal shape is:

```md
# Human Readable Title

## Summary

<short durable summary>

## Sources

- [[source-note-name]]

## Related pages

- [[another-note]]
```

### C. Index

Update `index.md` so every durable page touched by this add remains discoverable with a one-line description.

Treat `index.md` as a map-of-content. Use grouped `[[wikilinks]]` where appropriate, not just plain prose references.

### D. Log

Append a new entry to `log.md` using a parseable heading like:

**File mode:**
```md
## [YYYY-MM-DD] add (file) | <source title>
```

**Chat-context mode:**
```md
## [YYYY-MM-DD] add (chat) | <topic>
```

The entry should capture:

- the source path or conversation topic
- pages created or updated
- important contradictions, gaps, or follow-up leads

---

## Step 5 — Preserve uncertainty and provenance

The value of the wiki is not just compression. It is trustworthy synthesis.

Therefore:

- cite the local source path on the source page (file mode) or note the conversation date (chat-context mode)
- make uncertainty explicit
- separate the source's claims from the wiki's cross-source synthesis when they differ
- avoid overstating weak evidence
- never rewrite raw-source files

---

## Step 6 — Report back

After updating the wiki, output:

```md
Wiki updated from <source path or conversation topic>

### Mode

- file | chat

### Touched pages

- <path>
- <path>

### New pages

- <path>

### Index and log

- Index: <path>
- Log: <path>

### Open questions

- <question or "none">

### Next useful command

- `/do-wiki-add <another local source path or topic>`
```

If the source was already represented in the wiki and you refreshed it instead of creating new pages, say that explicitly.

---

## Rules

- File mode: local files only. Do not fetch URLs in this skill.
- Chat-context mode: synthesize from the current conversation context only. Do not fetch URLs.
- Prefer one source per run.
- Read the wiki schema before editing.
- Durable category notes use canonical kebab-case filenames.
- Internal note links use `[[kebab-case-note-name]]`.
- Update `index.md` and `log.md` on every add.
- Raw-source files are immutable.
- Do not leave avoidable broken wikilinks after the add pass.
- Strengthen reciprocal links when the relationship is materially useful.
- Make contradictions and stale claims explicit.
- Prefer incremental linked updates over large rewrites.
- In chat-context mode, conversation-sourced claims carry less authority than file-backed claims. Mark uncertainty explicitly.