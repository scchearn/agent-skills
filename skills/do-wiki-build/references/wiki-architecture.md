# Wiki Architecture Reference

Use this reference when scaffolding a persistent markdown wiki from the LLM Wiki pattern.

The target output is an Obsidian-friendly note graph:

- many small notes
- canonical kebab-case filenames for durable category notes
- `[[wikilinks]]` between durable notes
- backlinks and reciprocal relationships that make graph traversal useful
- `index.md` and `overview.md` as hub notes for both humans and LLMs

## Core layers

### Raw sources

- Immutable inputs.
- Examples: clipped articles, transcripts, PDFs, notes, datasets, images.
- The wiki may read from this layer but must never rewrite it.

### Wiki

- The maintained markdown layer between the raw sources and later answers.
- This is where summaries, topic pages, entity pages, comparison notes, and analyses live.
- The wiki is expected to compound over time.
- Prefer many small linked notes over a few monolithic summaries when durable nodes clearly exist.

### Schema

- The operating contract for future sessions.
- It defines page types, naming rules, linking rules, ingest behavior, and maintenance expectations.
- Without this layer, each future session has to rediscover how the wiki is supposed to work.

## Note identity and linking

- Use one canonical note per durable source, topic, entity, concept, or analysis.
- Durable category-note filenames should be kebab-case, such as `pricing-strategy.md`. Special root files like `index.md`, `overview.md`, `log.md`, and `SCHEMA.md` keep their fixed names.
- Internal note links should use `[[pricing-strategy]]`.
- H1 headings can remain human-readable, such as `# Pricing Strategy`.
- When a note becomes important enough to reuse, give it a real note instead of repeating the same explanation across many files.
- Prefer reciprocal links for meaningful relationships, especially between source notes and the topic/entity/concept notes they materially inform.

## Default scaffold

Use the smallest structure that still makes the wiki operable. A good default is:

```text
raw/
  README.md
  assets/
wiki/
  SCHEMA.md
  overview.md
  index.md
  log.md
  sources/
  topics/
  entities/
  concepts/
  analyses/
```

## Special files

### `overview.md`

- top-level orientation page
- wiki scope and intended corpus
- current state and open questions
- starting topic map
- wikilink hub for the major areas of the graph

### `index.md`

- home map-of-content and content-oriented catalog
- each page should have a link and one-line description
- organize by section so future sessions can quickly identify relevant pages
- keep this current on every ingest
- do not rely on it as the only navigation layer; the note graph should still be traversable from the notes themselves

### `log.md`

- chronological, append-only history
- record scaffold creation, ingests, query write-backs, and lint passes
- parseable headings make this searchable with simple tools later

Recommended heading format:

```md
## [2026-04-09] ingest | Source Title
```

## Page categories

These are common categories, not mandatory ones:

- `sources/` — one page per ingested source or source bundle
- `topics/` — broader subjects or themes that synthesize across sources
- `entities/` — people, companies, products, places, projects, or systems
- `concepts/` — ideas, methods, claims, frameworks, recurring patterns
- `analyses/` — query outputs worth preserving as first-class wiki pages

Use only the categories the domain actually needs.

Each note in these categories should use a canonical kebab-case filename and participate in the graph through outbound and inbound links where appropriate.

## Authoring guidance

- Prefer links over duplicated prose.
- Prefer `[[wikilinks]]` over plain prose references when a note exists.
- Make contradictions explicit.
- Separate confirmed facts from open questions.
- Do not over-scaffold with placeholder files.
- Keep starter content structural rather than synthetic when sources have not been ingested yet.
- Avoid isolated notes: new durable notes should be reachable from a hub note or a closely related note.

## Build-skill boundary

The scaffold pass should create the lanes, not drive every future workflow.

During build:

- define the raw-source location
- define the wiki structure
- define the schema
- initialize `index.md` and `log.md`
- establish the canonical link and filename rules
- create only the starter pages required for orientation

During ingest:

- read one or more new sources
- update source pages and topic/entity/concept pages
- strengthen the note graph with wikilinks and reciprocal links
- revise the index
- append the log
- note contradictions and open questions

During research, planning, execution, and amendment:

- consult the wiki when it exists, but do not require one
- treat the wiki as a durable memory layer, not the authority over the current repo state
- if current repo state or primary docs conflict with the wiki, trust the current repo state and update the wiki if the finding is durable
- file back only durable findings such as stable architecture facts, domain clarifications, recurring debugging discoveries, reusable comparisons, or established constraints
- avoid writing back ephemeral task chatter, temporary dead ends, or narrow planning noise
