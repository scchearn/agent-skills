---
name: do-wiki-build
description: Create or extend an Obsidian-friendly markdown wiki in the current workspace. Use this when the user wants to "build a wiki", "set up a knowledge base", "create a research vault", or scaffold a living markdown knowledge base before ingesting sources.
argument-hint: <topic, corpus, or wiki goal>
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, Write, Edit, AskUserQuestion, Skill
---

You are a senior engineer and knowledge-base architect working in the current workspace. Your job is to instantiate the LLM Wiki pattern as a durable markdown artifact, not to ingest a full corpus yet.

Follow the framework closely:

- **Raw sources** are immutable inputs the agent reads but never rewrites.
- **The wiki** is the maintained markdown layer between the agent and the raw sources.
- **The schema** is the operating contract that tells future sessions how to maintain the wiki consistently.

Your deliverable is a minimal, usable wiki scaffold that future sessions can grow safely.

The target shape is an Obsidian-friendly note graph:

- many small markdown notes instead of a few large files when durable topics, entities, or concepts exist
- canonical kebab-case filenames for durable category notes such as `pricing-strategy.md`
- internal links written as `[[pricing-strategy]]`
- strong cross-links and reciprocal backlinks
- `index.md` and `overview.md` acting as map-of-content hub notes, not just flat catalogs

## Input

The wiki goal is: $ARGUMENTS

---

## Step 1 — Inspect the workspace first

Before creating anything, inspect the current workspace so the wiki fits the repo instead of fighting it.

Read the most relevant files you can find, including when present:

1. Root guidance such as `CLAUDE.md`, `AGENTS.md`, `README.md`, or similar instructions
2. Existing documentation or note directories such as `wiki/`, `knowledge/`, `vault/`, `notes/`, `docs/`, `research/`, `raw/`, or `sources/`
3. Existing index or log files such as `index.md`, `log.md`, topic maps, or other wiki-like entry points

Determine:

- whether a wiki-like structure already exists
- whether there is already a clear raw-source directory
- whether there is an existing schema or operator guide you should extend instead of duplicating

Once you choose the locations, treat them as `<raw root>` and `<wiki root>` for the rest of this skill.

Prefer extending an existing wiki over creating a parallel one.

If the workspace already contains a clear wiki with `index.md`, `log.md`, and page directories, treat this skill as a structural refinement pass: add only the missing pieces needed to align it with the LLM Wiki pattern.

---

## Step 2 — Choose the smallest correct structure

Use the current workspace conventions when they are clear. If they are not clear, default to this structure:

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

Use these rules:

1. `raw/` is for immutable source material. The wiki must never edit files there.
2. `<wiki root>/` is the maintained markdown note graph.
3. Durable category-note filenames are kebab-case, such as `pricing-strategy.md`, `market-expansion.md`, or `acme-q1-shareholder-letter.md`. Special root files like `index.md`, `overview.md`, `log.md`, and `SCHEMA.md` keep their fixed names.
4. Internal note links use `[[kebab-case-note-name]]`.
5. Each durable concept, entity, topic, source, or analysis gets one canonical note. Prefer updating the existing note over creating synonyms.
6. `<wiki root>/index.md` is the home map-of-content and content catalog.
7. `<wiki root>/overview.md` is the top-level orientation page and high-level hub for the topic or corpus.
8. `<wiki root>/log.md` is the append-only chronological record.
9. `<wiki root>/SCHEMA.md` is the maintenance contract future sessions should follow.

Only create category directories that are justified. The default set above is recommended, but if the workspace is clearly narrower, keep it smaller.

---

## Step 3 — Read the bundled references

Before writing files, read:

1. `${CLAUDE_SKILL_DIR}/references/wiki-architecture.md`
2. `${CLAUDE_SKILL_DIR}/references/schema-template.md`

Use them as templates, but adapt them to the current workspace and the user's stated wiki goal.

---

## Step 4 — Build or refine the wiki scaffold

Create or update the wiki so it is immediately usable.

### A. Raw source layer

Ensure there is a clear raw-source location.

- If the workspace already has a good raw-source directory, reuse it.
- Otherwise create `raw/` and `raw/assets/`.
- Create or update `raw/README.md` so it clearly states that files in `raw/` are source-of-truth inputs and must remain immutable.

### B. Schema layer

Create or update `<wiki root>/SCHEMA.md`.

It must explain, in workspace-specific language:

1. the purpose and scope of the wiki
2. the directory layout
3. the page types that exist in this wiki
4. the canonical note classes and their filename patterns
5. how pages should link to each other using `[[kebab-case-note-name]]`
6. how reciprocal backlinks should be maintained
7. how `index.md` and `overview.md` act as map-of-content hub notes
8. how `index.md` and `log.md` must be maintained
9. how ingest should work
10. how query outputs can be filed back into the wiki
11. how non-wiki workflow skills like research, planning, execution, and plan amendment can consult the wiki and file durable findings back into it
12. how lint or health-check passes should look
13. the rule that raw sources are immutable
14. the rule that uncertainty, contradictions, and stale claims must be made explicit instead of silently overwritten

If there is an existing root guidance file such as `CLAUDE.md` or `AGENTS.md`, add a brief pointer to `<wiki root>/SCHEMA.md` only when you can do so cleanly without overwriting unrelated instructions. Keep that root-file edit short.

### C. Wiki layer

Create or update these core files:

- `<wiki root>/overview.md`
- `<wiki root>/index.md`
- `<wiki root>/log.md`

Use the chosen wiki root instead of the literal `wiki/` path when the workspace already follows a different convention.

Requirements:

#### `<wiki root>/overview.md`

- State the wiki's scope and intended corpus.
- Distinguish between confirmed current sources and expected future sources.
- Capture a short starting map of major topic areas.
- Use `[[kebab-case-note-name]]` links for major topics or starter notes that exist after the build.
- Include open questions if the scope is still fuzzy.

#### `<wiki root>/index.md`

- Make it the home map-of-content for the wiki, not just a flat catalog.
- Group entries by page type or section such as overview, sources, topics, entities, concepts, and analyses.
- Use `[[kebab-case-note-name]]` links wherever the linked note exists.
- Give each listed page a one-line description.
- Make it easy to traverse in Obsidian and easy to skim in plain markdown.
- If the wiki is new, seed the file with the pages created during this run.

#### `<wiki root>/log.md`

- Make it append-only.
- Add an initial entry for the scaffold creation using a parseable heading like:

```md
## [YYYY-MM-DD] build | Initial wiki scaffold
```

- Note the chosen structure and any non-obvious decisions.

### D. Starter pages and directories

Create any category directories needed by the chosen structure.

Add starter pages only when they improve orientation. Prefer a minimal scaffold over lots of empty placeholders.

When you create starter topic, entity, concept, or analysis notes:

- use canonical kebab-case filenames
- use human-readable H1 titles inside the files
- add meaningful outbound `[[wikilinks]]`
- avoid isolated files by linking each new note from `index.md`, `overview.md`, or another durable note

Examples of acceptable starter pages:

- a topic map page if the wiki goal has clear subdomains
- a source intake notes page if there is already a curated raw corpus
- a short analyses page if the user explicitly asked for synthesis outputs

Do not fabricate content that should come from later source ingestion.

---

## Step 5 — Keep the scaffold factual

This skill creates structure, not compiled knowledge.

Important boundaries:

- Do not ingest or summarize the entire corpus yet.
- Do not treat random repo docs as canonical sources unless the user clearly wants them included in the wiki corpus.
- If you mention likely future sources or themes, label them as expectations, candidates, or open questions.
- If the workspace already has content that clearly belongs in the wiki, you may link to it or note it as a candidate source, but do not silently convert it into fully synthesized wiki knowledge in this step.

---

## Step 6 — Offer Obsidian vault setup

After the scaffold is written, use `AskUserQuestion` to ask:

> "Would you like to set up an Obsidian vault at the wiki root (`<wiki root>`) now? This will configure Obsidian settings, plugins, and standard folders."

- If the user answers **yes**: invoke the `setup-obsidian-vault` skill with the wiki root path as the argument.
- If the user answers **no**: proceed directly to Step 7.

---

## Step 7 — Report back

After writing the scaffold, output:

```md
Wiki scaffold ready at <wiki root>

### Created or updated

- <path>
- <path>

### Structure decisions

- Raw sources: <path>
- Wiki root: <path>
- Schema: <path>
- Root guidance link: <path or "none">

### Next step

- `/do-wiki-add <local source path or topic>`
```

If you found an existing wiki and only refined it, say so explicitly in the report.

---

## Rules

- Prefer extending an existing wiki over creating a second one.
- Keep the structure small and obvious.
- Prefer many small linked notes over monoliths when durable nodes exist.
- Raw-source files are immutable.
- Internal links use `[[kebab-case-note-name]]`.
- Durable category notes use canonical kebab-case filenames. Special root files keep their fixed names.
- Avoid creating isolated durable notes that are only visible from the file tree.
- `<wiki root>/log.md` is append-only.
- `<wiki root>/index.md` must be useful immediately after this skill runs.
- Do not perform full source ingestion in this skill. That belongs to `/do-wiki-add`.
- If the workspace is too ambiguous to place the wiki safely, ask the smallest follow-up question needed.
