---
name: do-wiki-ingest
description: Read a local source file and integrate it into an existing Obsidian-friendly markdown wiki. Use this when the user wants to "ingest a source", "add this document to the wiki", or update the wiki from a local note, transcript, article, report, or PDF.
argument-hint: <local source path or source description>
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, Write, Edit
---

You are a senior engineer and disciplined wiki maintainer working in the current workspace. Your job is to compile a local source into the persistent wiki so future sessions inherit the knowledge instead of rediscovering it.

The target output is an Obsidian-friendly note graph:

- canonical kebab-case filenames for durable category notes
- internal links written as `[[kebab-case-note-name]]`
- small linked source, topic, entity, concept, and analysis notes
- reciprocal backlinks where the relationship is materially useful

This skill is for **local-file ingestion only**. Do not fetch URLs directly in this version.

## Input

The source to ingest is: $ARGUMENTS

---

## Step 1 — Resolve the source and the wiki

Start by locating both the source and the target wiki.

### Source resolution

Use the argument plus the current workspace to identify the source file or a very small intended batch of related files.

Rules:

1. Prefer an exact local path when the user provided one.
2. If the user gave a title or description instead of a path, search for the best candidate under likely source locations such as `raw/`, `sources/`, `research/`, `docs/`, `notes/`, or similar directories.
3. If more than one candidate is plausible, ask the smallest follow-up question needed.
4. If the user clearly requested a large batch, stop and ask them to narrow it or to run repeated ingests. This skill is optimized for one source at a time.

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

Read the source carefully.

Distinguish:

- **facts** directly supported by the source
- **claims or interpretations** made by the source
- **open questions** the source raises but does not settle
- **entities, concepts, and topics** that should be linked into the wiki

When the source is large, read enough of it to produce a faithful summary and then use targeted follow-up reads for the most relevant sections.

If the file format is not readable with the available tools, stop and say exactly what prevented ingestion.

---

## Step 4 — Update the wiki incrementally

Treat the wiki as a compiled artifact that must stay internally coherent.

### A. Source page

Create or update a source summary page under `wiki/sources/` or the workspace's equivalent source-page directory.

Use the bundled template as a starting point and adapt it to the schema already in use.

The source note should use a canonical kebab-case filename. The H1 title inside the note can remain human-readable.

Every durable `[[wikilink]]` on the source page should resolve to an existing canonical note or to a note you create or update during the same ingest pass.

If a page for this source already exists:

- update it in place
- preserve useful prior links or notes
- do not duplicate it under a second filename

### B. Related pages

Update the most relevant topic, entity, concept, or analysis pages.

Graph fan-out is required, not optional. Each ingest should leave behind a navigable local graph around the source, not just a standalone source summary.

Use these rules:

1. Only touch pages materially affected by the source.
2. If an important entity, concept, or topic is central to the source and still lacks a dedicated page, create a minimal canonical note for it using a kebab-case filename.
3. Prefer small linked updates over copying the same summary text into many pages.
4. If the new source contradicts an existing page, make the conflict explicit instead of silently replacing the older view.
5. If the new source supersedes an old claim, say so clearly and point to both sources.
6. When the source page links to a topic, entity, concept, or analysis note, update that note to link back under `Sources`, `Mentioned in`, or `Related pages` when the relationship is materially useful.
7. Avoid isolated durable notes. Every new note should be reachable from `index.md`, `overview.md`, or another durable note, and should contain at least one meaningful outbound link.

When you create a new topic, entity, or concept note during ingest, keep it small but structured. A good minimal shape is:

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

Update `index.md` so every durable page touched by this ingest remains discoverable with a one-line description.

Treat `index.md` as a map-of-content. Use grouped `[[wikilinks]]` where appropriate, not just plain prose references.

### D. Log

Append a new entry to `log.md` using a parseable heading like:

```md
## [YYYY-MM-DD] ingest | <source title>
```

The entry should capture:

- the source path
- pages created or updated
- important contradictions, gaps, or follow-up leads

---

## Step 5 — Preserve uncertainty and provenance

The value of the wiki is not just compression. It is trustworthy synthesis.

Therefore:

- cite the local source path on the source page
- make uncertainty explicit
- separate the source's claims from the wiki's cross-source synthesis when they differ
- avoid overstating weak evidence
- never rewrite raw-source files

---

## Step 6 — Report back

After updating the wiki, output:

```md
Wiki updated from <source path>

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

- `/do-wiki-ingest <another local source path>`
```

If the source was already represented in the wiki and you refreshed it instead of creating new pages, say that explicitly.

---

## Rules

- Local files only. Do not fetch URLs in this skill.
- Prefer one source per run.
- Read the wiki schema before editing.
- Durable category notes use canonical kebab-case filenames.
- Internal note links use `[[kebab-case-note-name]]`.
- Update `index.md` and `log.md` on every ingest.
- Raw-source files are immutable.
- Do not leave avoidable broken wikilinks after the ingest pass.
- Strengthen reciprocal links when the relationship is materially useful.
- Make contradictions and stale claims explicit.
- Prefer incremental linked updates over large rewrites.
