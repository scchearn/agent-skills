# Agent Workflow Skill Framework

Reusable workflow skills for agent coding harnesses compatible with Anthropic's skill layout.

These skills help an agent research a problem, save the findings, turn them into a concrete plan, execute that plan, update that plan when the work changes, and build persistent markdown wikis from source material.

The wiki skills are designed for an Obsidian-style environment: durable notes use kebab-case filenames, internal links use `[[wikilinks]]`, and the workflow prefers many small connected notes over large isolated summaries.

When a wiki already exists, the non-wiki workflow skills can consult it as a durable memory layer and may write back only findings that are worth preserving. Current repo state and primary docs still win if the wiki is stale or wrong.

## Installation

Use the matching global or project-level setup below.

### Claude Code

Claude Code looks for skills in:

- `.claude/skills/` inside a project
- `~/.claude/skills/` for your personal global skills

Project install:

```bash
mkdir -p .claude/skills
cp -R skills/* .claude/skills/
```

Global install:

```bash
mkdir -p ~/.claude/skills
cp -R skills/* ~/.claude/skills/
```

### Codex

Codex's normal skills directory is:

- `~/.codex/skills/` for your personal skills

Project level:

Codex does not document a native project-local skills folder the same way Claude Code, OpenCode, and `pi` do. For project-specific behavior, use `AGENTS.md` in the repo.

Global install:

```bash
mkdir -p ~/.codex/skills
cp -R skills/* ~/.codex/skills/
```

### OpenCode

OpenCode's native skill locations are:

- `.opencode/skills/` inside a project
- `~/.config/opencode/skills/` for your personal global skills

Project install:

```bash
mkdir -p .opencode/skills
cp -R skills/* .opencode/skills/
```

Global install:

```bash
mkdir -p ~/.config/opencode/skills
cp -R skills/* ~/.config/opencode/skills/
```

OpenCode can also read `.claude/skills`, but `.opencode/skills` is the native location.

### `pi`

`pi` looks for skills in:

- `.pi/skills/` inside a project
- `~/.pi/agent/skills/` for your personal global skills

Project install:

```bash
mkdir -p .pi/skills
cp -R skills/* .pi/skills/
```

Global install:

```bash
mkdir -p ~/.pi/agent/skills
cp -R skills/* ~/.pi/agent/skills/
```

`pi` can also load extra skill paths from `.pi/settings.json` or `~/.pi/agent/settings.json`.

## Quick Start

Once the skills are available, a typical flow looks like this:

```text
/do-research how this app authenticates users
/do-plan add audit logging for failed sign-ins
/do-start plans/audit-logging-failed-sign-ins.md
/do-amend plans/audit-logging-failed-sign-ins.md

/do-wiki-build build a company research wiki for local source files
/do-wiki-add raw/acme-q1-shareholder-letter.md
/do-wiki-add decisions from today's architecture review
/do-wiki-lint
/do-wiki-query what changed in the latest quarter?
```

`/do-research` writes a reusable research note to `plans/research/`. Later, `/do-plan` looks for relevant research notes there before it creates a plan.

Many harnesses expose skills as slash commands. If yours uses a different invocation style, use the same skill names and arguments in the format your harness expects.

If you already have an existing wiki-like notes corpus instead of starting from scratch, use `/do-wiki-align [root]` before your first lint or query pass.

## Wiki Conventions

The wiki workflow assumes these defaults unless the workspace already has a stronger established convention:

- durable note filenames use kebab-case such as `pricing-strategy.md`
- special root files keep their fixed names: `index.md`, `overview.md`, `log.md`, and `SCHEMA.md`
- internal links use `[[pricing-strategy]]`
- H1 titles can remain human-readable, such as `# Pricing Strategy`
- `index.md` and `overview.md` act as map-of-content hub notes
- source, topic, entity, concept, and analysis notes should cross-link and, when appropriate, link back to each other
- the goal is a graph of small reusable notes that is easy to traverse in Obsidian and easy for an LLM to inspect
- `AGENTS.md` / `CLAUDE.md` files should stay lean operational entrypoints and point into the wiki for deep, fast-aging reference material

## Which Skill Should I Use?

| Skill | Use it when | What you get |
| --- | --- | --- |
| [agent-md-improver](./skills/agent-md-management/skills/agent-md-improver/) | You want to audit or improve `AGENTS.md`, `CLAUDE.md`, or local guidance files so future agent sessions have better working context | A quality report plus targeted guidance-file updates after approval, with `AGENTS.md` preferred for shared neutral guidance and `CLAUDE.md` supported when present |
| [do-research](./skills/do-research/) | You need evidence before deciding what to build or change | A research note in `plans/research/` with findings, constraints, and the next recommended step, plus optional durable wiki updates when warranted |
| [do-plan](./skills/do-plan/) | You have a feature, bug fix, or task that should be broken into ordered steps | A plan file in `plans/` with concrete tasks, verification steps, links to any research it used, and optional durable wiki updates when warranted |
| [do-start](./skills/do-start/) | You want the agent to execute the next unblocked task from a plan | Autonomous task execution with verification, plan updates, and optional durable wiki write-back |
| [do-amend](./skills/do-amend/) | A plan already exists, but requirements or scope changed | An impact analysis, then a safe update to the plan after approval, with optional durable wiki updates when warranted |
| [do-wiki-build](./skills/do-wiki-build/) | You want a persistent markdown wiki scaffold for a topic, corpus, or research area | An Obsidian-friendly wiki structure with raw-source, schema, map-of-content, log, and note-graph conventions ready for future ingestion |
| [do-wiki-align](./skills/do-wiki-align/) | You already have a wiki-like notes corpus and want it normalized to the repo conventions | A proposal-first retrofit pass that aligns structure, naming, hub notes, links, and relevant AGENTS/CLAUDE files to the Obsidian-friendly note graph model |
| [do-wiki-add](./skills/do-wiki-add/) | You already have a wiki and want to add a local source or conversation context to it | Updated source, topic, entity, and concept notes (from a local file or conversation context) plus backlink-aware graph expansion, refreshed `index.md`, and a log entry |
| [do-wiki-lint](./skills/do-wiki-lint/) | You want to health-check the wiki for contradictions, stale claims, unresolved wikilinks, or graph drift | A maintenance pass that applies safe graph fixes, updates the log, and calls out unresolved gaps |
| [do-wiki-amend](./skills/do-wiki-amend/) | The agent discovers during a conversation that the wiki is wrong, stale, incomplete, or contradicted by new evidence — or the user says "the wiki is wrong about X" | A proposal-first corrective pass that corrects factual errors, marks superseded claims, completes incomplete pages, or surfaces contradictions — with history preservation for high-severity fixes |
| [do-wiki-review](./skills/do-wiki-review/) | You want to see what's still open, unresolved, or needs attention in the wiki | A read-only report classifying all open questions, unresolved contradictions, stale claims, and knowledge gaps by urgency and topic — no edits, no new pages |
| [do-wiki-query](./skills/do-wiki-query/) | You want an answer grounded in the wiki, or a reusable synthesis filed back into it | A cited answer from the current wiki plus an optional linked analysis page, related-note updates, index refresh, and log entry when the result has durable value |
| [setup-obsidian-vault](./skills/setup-obsidian-vault/) | You want to scaffold a new Obsidian vault with your standard setup | A new vault directory with `.obsidian/` config, hotkeys, and standard folders (Journals, Templates, Attachments) — open in Obsidian and allow plugin installs |

## Recommended Workflow

1. Start with `/do-research <topic>` when the workspace or external behavior is unclear.
   Example: `/do-research how billing webhooks are validated today`
2. Run `/do-plan <task description>` to create a plan. It will reuse relevant notes from `plans/research/` when they exist.
3. Run `/do-start plans/<slug>.md` to execute the plan in order.
4. Run `/do-amend plans/<slug>.md` if new requirements appear or completed work needs to be revisited.

For knowledge-base workflows, a typical flow is:

1. Run `/do-wiki-build <topic or corpus>` to scaffold the wiki.
2. If you already have a wiki-like notes corpus, run `/do-wiki-align [root]` first instead of rebuilding from scratch.
3. Run `/do-wiki-add <local source path or topic>` repeatedly to compile sources into it.
4. Run `/do-wiki-lint` periodically to health-check the wiki and keep structure and synthesis aligned.
5. Run `/do-wiki-amend <what changed>` when you discover the wiki is wrong, stale, incomplete, or contradicted by new evidence. The agent may also auto-trigger this skill during a conversation when it detects a mismatch between wiki content and current reality.
6. Run `/do-wiki-query <question>` to answer from the wiki and optionally save durable analyses back into it.
7. Run `/do-wiki-review [scope]` to surface all open questions, unresolved contradictions, and knowledge gaps classified by urgency and topic.

If you only want to audit or trim `AGENTS.md` / `CLAUDE.md` files so they point to the wiki better, use:

1. `/do-wiki-align --guidance-only`
2. `/do-wiki-align --guidance-only <app, package, service, or subtree>`

In `--guidance-only` mode, the wiki is read-only context only. The skill should update only the relevant `AGENTS.md` / `CLAUDE.md` files and should not rename, move, or edit wiki pages.

You do not need to use every skill every time. If the work is already well understood, it is fine to start with `/do-plan`.

If `plans/research/` or a wiki already exists, `/do-research`, `/do-plan`, `/do-start`, and `/do-amend` can reuse that state. They should only write back into the wiki when the finding is durable enough to help future sessions.

## Common Examples

```text
/do-research how billing webhooks are validated today
/do-plan add CSV export to the admin orders page
/do-start plans/admin-orders-csv-export.md
/do-start plans/admin-orders-csv-export.md T3
/do-start plans/admin-orders-csv-export.md T3,T5
/do-start plans/admin-orders-csv-export.md T3-T5

/do-wiki-build build a competitor intelligence wiki
/do-wiki-add raw/competitors/acme-launch-post.md
/do-wiki-add raw/competitors/acme-q1-earnings.pdf
/do-wiki-add why we chose SQLite over Postgres for caching
/do-wiki-lint competitors
/do-wiki-amend the API now uses Bearer tokens, not Basic auth
/do-wiki-query how does acme's launch strategy differ from last quarter?
/do-wiki-review

/do-wiki-align docs/knowledge
/do-wiki-align --guidance-only apps/api
/do-wiki-lint docs/knowledge
```

For `/do-amend`, describe the change first, then invoke the skill against the existing plan:

```text
We now need the CSV export to include refunded orders too.
/do-amend plans/admin-orders-csv-export.md
```

## What These Skills Create

- `plans/research/<slug>.md` for reusable research findings
- `plans/<slug>.md` for the working plan
- `plans/INDEX.md` for a simple overview of every plan, so you can quickly see what is pending, in progress, or done
- `raw/` and `wiki/` structures when you use the wiki-building workflow
- Obsidian-style wiki notes with kebab-case filenames and `[[wikilinks]]`
- optional durable wiki updates from research, planning, execution, and amendment workflows when a wiki already exists
- conversation-sourced notes (marked with `Source type: conversation`) when using `/do-wiki-add` in chat-context mode
- corrective amendments with history preservation (strikethrough old claims, superseded markers, contradiction notes) when using `/do-wiki-amend`
- normalized hub notes, link styles, durable note identities, and leaner guidance-file pointers when you use the wiki-alignment workflow
- appended `wiki/log.md` lint entries and wiki maintenance edits when you use the wiki-lint workflow
- optional `wiki/analyses/` pages plus query log entries when you use the wiki-query workflow
- a read-only review report classifying open questions, unresolved contradictions, stale claims, and knowledge gaps by urgency (needs-research, needs-ingest, needs-decision, low-priority) and topic when you use `/do-wiki-review`

This keeps research, planning, and execution state in the repository instead of burying it in chat history. Research notes live alongside plans, while `plans/INDEX.md` gives you one place to check overall plan progress.

## Typical Structure

After you start using the framework, you will typically have:

```text
.
├── README.md
├── plans/
│   ├── INDEX.md
│   └── research/
└── skills/
    ├── do-amend/
    │   └── SKILL.md
    ├── do-plan/
    │   └── SKILL.md
    ├── do-research/
    │   └── SKILL.md
    ├── do-wiki-build/
    │   └── SKILL.md
    ├── do-wiki-align/
    │   └── SKILL.md
    ├── do-wiki-add/
    │   └── SKILL.md
    ├── do-wiki-amend/
    │   └── SKILL.md
    ├── do-wiki-lint/
    │   └── SKILL.md
    ├── do-wiki-query/
    │   └── SKILL.md
    ├── do-wiki-review/
    │   └── SKILL.md
    ├── do-start/
    │   └── SKILL.md
    └── setup-obsidian-vault/
        ├── SKILL.md
        ├── scripts/
        │   └── setup.sh
        └── assets/
            └── obsidian/
```

## Naming Collisions

Different agent coding harnesses handle duplicate skill names differently. Some prefer higher-precedence locations. Others warn and use the last-loaded definition.

The safe rule is simple: keep skill names unique across your global, shared, and project-level skill sets. Do not rely on collision behavior as an override mechanism.
