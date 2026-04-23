# Wiki Schema Template

Adapt this template into `<wiki root>/SCHEMA.md`. Keep it specific to the workspace and topic.

```md
# Wiki Schema

## Purpose

This wiki exists to maintain a persistent, incrementally refined knowledge base about <topic or corpus>.

The raw-source layer is stored in `<raw path>`. Those files are immutable.
The wiki layer is stored in `<wiki path>`. Those files are maintained by the agent.

## Directory layout

- `<raw path>` — immutable source documents
- `<wiki path>/index.md` — root hub with a concise `## Overview` section plus page catalog with one-line summaries
- `<wiki path>/log.md` — append-only activity log
- `<wiki path>/sources/<source-slug>.md` — source summary pages
- `<wiki path>/topics/<topic-slug>.md` — topic synthesis pages
- `<wiki path>/entities/<entity-slug>.md` — entity pages
- `<wiki path>/concepts/<concept-slug>.md` — concept pages
- `<wiki path>/analyses/<analysis-slug>.md` — preserved query outputs and cross-source analyses

## Naming rules

- Durable category-note filenames are kebab-case.
- Special root files such as `index.md`, `log.md`, and `SCHEMA.md` keep their fixed names.
- Internal note links use `[[kebab-case-note-name]]`.
- H1 titles can be human-readable.
- One durable identity gets one canonical note. Do not create a second note for the same concept, entity, topic, source, or analysis when an appropriate canonical note already exists.
- Keep root-level orientation inside `index.md`. A separate root `overview.md` is legacy drift and should be consolidated into `index.md` rather than recreated.

## Page types

### Source pages

- One page per source added via `/do-wiki-add` or another explicitly approved source-add workflow.
- Record source path, scope, summary, key facts, open questions, and important outbound links.
- Link to relevant topic, entity, and concept notes using `[[wikilinks]]`.

### Topic pages

- Synthesize across multiple sources.
- Link to the most relevant source, concept, and entity pages.
- Include backlinks to the source and analysis pages that materially shape the topic.

### Entity pages

- Track a single real-world or in-domain entity.
- Update when new sources materially change the picture.
- Include a `Sources` or `Mentioned in` section with backlinks to source notes when appropriate.

### Concept pages

- Capture recurring ideas, frameworks, claims, or terminology.
- Include backlinks to the source, topic, or analysis notes that materially define the concept.

### Analysis pages

- Preserve valuable query outputs that should not disappear into chat history.
- Include `Related pages` wikilinks so analyses strengthen the graph instead of becoming isolated files.

## Linking rules

- Prefer internal `[[wikilinks]]` whenever a page already exists.
- When a concept or entity becomes important across multiple pages, create a dedicated canonical note instead of repeating the same explanation everywhere.
- When a source page links to a topic, entity, or concept note, update that note to link back under `Sources`, `Mentioned in`, or `Related pages` when the relationship is materially useful.
- Avoid isolated durable notes. New notes should be reachable from a hub note or a closely related note.
- Do not create links to pages that are unlikely to be used.

## Index rules

- `index.md` is the first file future sessions should read.
- `index.md` is the single root hub and a map-of-content, not just a flat catalog.
- Near the top, `index.md` should contain a concise `## Overview` section covering the wiki's scope, intended corpus, confirmed current sources versus expected future sources, major topic areas, and any durable open questions worth surfacing.
- Every durable page in the wiki should appear there with a one-line description.
- Use `[[wikilinks]]` in grouped sections so the index acts as a hub note.
- Keep entries grouped by section.

## Log rules

- `log.md` is append-only.
- Use parseable headings such as `## [YYYY-MM-DD] build | Initial wiki scaffold`, `## [YYYY-MM-DD] add (file) | <source title>`, and `## [YYYY-MM-DD] add (chat) | <topic>`.
- Record builds, `/do-wiki-add` runs, query write-backs, lint passes, and durable write-backs from research, planning, execution, and amendment workflows.

## `/do-wiki-add` rules

- Source-derived note creation belongs to `/do-wiki-add`, not `/do-wiki-build`.
- Read the source first.
- Update or create a source page.
- Update impacted topic, entity, concept, and analysis pages.
- Create missing canonical notes when a topic, entity, or concept is central to the added source or repeated enough to justify reuse.
- Ensure new and updated notes use `[[wikilinks]]` and reciprocal backlinks where appropriate.
- Revise `index.md`.
- Append a log entry.
- Make contradictions and uncertainty explicit.

## Query write-back rules

- If a query produces a durable comparison, synthesis, or explanation, file it into `analyses/` or the most appropriate existing page.
- Durable write-backs should cite supporting wiki pages, include `Related pages` wikilinks, and strengthen the existing note graph rather than creating isolated files.

## Other workflow write-back rules

- Non-wiki workflow skills such as research, planning, execution, and plan amendment may read the wiki when it exists, but they must still verify current repo state directly.
- If the wiki conflicts with current repo state, source code, or primary documentation, trust the current repo state and update the wiki only when the correction is durable.
- Good durable write-backs include stable architecture facts, durable domain findings, recurring debugging discoveries, clarified terminology, reusable comparisons, and established workflow constraints.
- Do not write back ephemeral planning chatter, narrow task sequencing, temporary dead ends, or one-off local execution noise.
- Prefer updating an existing relevant topic, concept, entity, source, or analysis note over creating a new note.
- When a non-wiki workflow writes back, update `index.md` if durable pages changed and append `log.md` using a parseable heading such as:
  - `## [YYYY-MM-DD] research | <topic>`
  - `## [YYYY-MM-DD] plan | <feature>`
  - `## [YYYY-MM-DD] execution | <task or feature>`
  - `## [YYYY-MM-DD] amend | <plan or scope>`

## Lint rules

Periodically check for:

- missing or stale `## Overview` content in `index.md`
- unresolved `[[wikilinks]]`
- orphan pages
- notes with no meaningful outbound links
- stale claims
- contradictory statements across pages
- concepts mentioned repeatedly but lacking a page
- duplicate note identities under different filenames
- missing source links
- missing reciprocal backlinks where the relationship is clearly material
- outdated index entries
- a legacy root `overview.md` that should be consolidated into `index.md`

## Non-negotiable rules

- Never rewrite raw-source files.
- Never create a second canonical note for the same durable identity when an appropriate note already exists.
- Do not silently delete contradictions; explain them.
- Prefer incremental updates over large rewrites.
- Keep the wiki readable by humans and easy for LLMs to traverse without requiring search infrastructure.
- Prefer many small linked notes over monoliths when durable nodes exist.
```
