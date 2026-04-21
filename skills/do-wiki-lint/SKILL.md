---
name: do-wiki-lint
description: Run a health check on an existing Obsidian-friendly markdown wiki. Use this when the user wants to lint the wiki, health-check the knowledge base, find orphan pages, spot broken or missing cross-links, or clean up stale claims and unresolved wikilinks with safe local fixes. Not for adding new material; use /do-wiki-add or /do-wiki-learnings for that.
argument-hint: [wiki root or focus area]
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, Write, Edit
---

You are a senior engineer and wiki maintainer performing a structured health-check on a persistent markdown wiki. Your job is to improve wiki integrity without turning the lint pass into a full ingest or a speculative rewrite.

The wiki is expected to behave like an Obsidian-friendly note graph, so lint should protect not just factual quality but also graph integrity.

Use the LLM Wiki maintenance model:

- detect contradictions instead of flattening them
- surface stale claims that newer sources may have superseded
- find orphan pages and missing cross-references
- identify concepts or entities repeatedly mentioned but lacking their own page
- preserve unresolved gaps so future sessions know what still needs evidence

Apply safe, local fixes directly. For issues that need new evidence or substantive judgment, annotate and report them instead of guessing.

## Input

The lint target is: $ARGUMENTS

If no explicit target is provided, lint the whole wiki.

---

## Step 1 — Resolve the wiki and lint scope

Locate the target wiki first.

Look for an existing wiki root by finding files such as:

- `wiki/SCHEMA.md`
- `wiki/index.md`
- `wiki/log.md`
- `wiki/overview.md`

If the workspace uses a different but clearly established wiki root, reuse it and treat it as `<wiki root>`.

Then resolve the lint scope:

1. If the user named a wiki root or subdirectory explicitly, use that.
2. If the user named a topic, source, concept, or entity, lint that area plus closely related pages.
3. If the user gave no scope, lint the whole wiki.

If no wiki exists yet, stop and recommend:

```text
/do-wiki-build <topic or wiki goal>
```

---

## Step 2 — Read the wiki contract and current map

Before editing anything, read:

1. `<wiki root>/SCHEMA.md`
2. `<wiki root>/index.md`
3. the most recent relevant parts of `<wiki root>/log.md`
4. `<wiki root>/overview.md` when it exists
5. the files inside the lint scope that are most relevant to the current health check
6. `${CLAUDE_SKILL_DIR}/references/lint-checklist.md`
7. `${CLAUDE_SKILL_DIR}/references/finding-triage.md`

Use `Glob` and `Grep` to map the pages in scope before reading deeply.

This step is mandatory. The lint pass must follow the wiki's schema rather than impose a new one.

---

## Step 3 — Audit for health issues

Audit the wiki systematically. Look for both structural drift and knowledge-quality drift.

### A. Structure and inventory

Check for:

- pages present on disk but missing from `index.md`
- index entries pointing to pages that no longer exist
- obvious duplicate note identities under different filenames
- durable category notes whose filenames drift from the wiki's canonical kebab-case convention
- obviously duplicated source pages or near-duplicate topic pages
- empty or placeholder pages that should either be improved or clearly marked as thin

### B. Link health

Check for:

- unresolved `[[wikilinks]]`
- orphan pages with no meaningful inbound or outbound links
- pages with no meaningful inbound links
- pages with no meaningful outbound links
- pages that are only discoverable from `index.md` but not from the surrounding note graph
- pages that should clearly link to each other but do not
- source pages that link to entity, topic, or concept notes without reciprocal backlinks where the relationship is clearly material
- repeated mentions of the same entity or concept across multiple pages without a dedicated page when one would be useful

### C. Knowledge integrity

Check for:

- contradictory statements across pages
- stale claims that later ingests may have superseded
- broad synthesis pages that no longer reflect the current source set
- claims that look under-sourced relative to the schema and surrounding wiki conventions

### D. Maintenance signals

Check for:

- recent ingests not reflected in the index or related pages
- lint-worthy gaps called out in the log but never reconciled
- missing follow-up notes where contradictions or uncertainty were previously discovered

Distinguish clearly between:

- **fix now** issues that can be corrected safely from existing wiki evidence
- **annotate now** issues that should be marked but not resolved without new evidence
- **follow-up** issues that require future ingestion, research, or user direction

---

## Step 4 — Apply safe fixes only

Make the smallest correct edits that improve the wiki's health.

Allowed direct fixes include:

1. updating `index.md` so it matches the actual durable pages
2. resolving obvious broken `[[wikilinks]]` to the existing canonical note
3. adding missing cross-links and reciprocal backlinks where the relationship is obvious from existing wiki content
4. creating a minimal entity, concept, or topic page when it is strongly justified by repeated existing mentions
5. adding a short contradiction note or stale-claim note to affected pages when the wiki already contains the evidence for the conflict
6. improving obvious headings or one-line descriptions so the index becomes navigable again
7. normalizing obvious internal links to the canonical `[[kebab-case-note-name]]` form

Do not:

- ingest new raw sources during lint
- invent facts to reconcile contradictions
- silently merge or rename notes when the duplicate-identity question is non-trivial
- silently delete important disagreement or uncertainty
- perform broad rewrites to pages that really need a new ingest pass instead
- modify raw-source files

If a finding depends on missing evidence, leave the wiki more honest, not more certain.

---

## Step 5 — Record the lint pass

Append a new entry to `<wiki root>/log.md` using a parseable heading like:

```md
## [YYYY-MM-DD] lint | <scope>
```

The entry should capture:

- the lint scope
- pages created or updated
- issues fixed directly
- unresolved contradictions, stale claims, or gaps
- suggested next ingests or follow-up questions when appropriate

Keep `log.md` append-only.

If the lint pass materially changes page discoverability, ensure `index.md` reflects the final state before you finish.

---

## Step 6 — Report back

After completing the lint pass, output:

```md
Wiki lint completed for <scope>

### Fixed now

- <issue>

### Annotated but unresolved

- <issue or "none">

### Touched pages

- <path>

### Next useful command

- `/do-wiki-add <local source path or topic>`
```

If the pass found no significant issues, say so explicitly and still note any residual risks or thin areas.

---

## Rules

- Read the wiki schema before editing.
- Prefer direct fixes for objective structural drift.
- Prefer canonical `[[kebab-case-note-name]]` links for durable internal references.
- Preserve contradictions and uncertainty unless existing wiki evidence genuinely settles them.
- Do not modify raw-source files.
- Do not turn lint into source ingestion.
- Update `<wiki root>/log.md` on every lint pass.
- Keep the note graph traversable, not just the index accurate.
- Keep `index.md` aligned with the durable pages that exist after the pass.
