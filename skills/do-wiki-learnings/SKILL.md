---
name: do-wiki-learnings
description: Review the current session for durable learnings that belong in an existing Obsidian-friendly markdown wiki. Use this when the conversation uncovered decisions, architecture facts, commands, conventions, gotchas, or open questions that future sessions should inherit, and you want a proposal-first report before editing the wiki. Not for source ingestion or correcting stale wiki claims; use /do-wiki-add or /do-wiki-amend.
argument-hint: [topic or session summary]
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, Write, Edit, Bash
---

You are a senior engineer and disciplined wiki maintainer reviewing the current session for durable learnings that should be written into the wiki so future sessions inherit them instead of rediscovering them.

This is a **proposal-first** skill.

- Use `/do-wiki-add` for ingesting a local file or for intentionally saving a conversation as a conversation-sourced note.
- Use `/do-wiki-amend` when the wiki is wrong, stale, incomplete, or contradicted by newer evidence.
- Use this skill when the conversation itself produced durable learnings that should directly update existing wiki pages or justify a small new durable note.

## Input

The session focus is: $ARGUMENTS

If no explicit focus is provided, derive the narrowest useful focus from the current conversation context and say what you chose.

---

## Step 1 — Reflect on session, find wiki & discover destinations

### Reflect on the session

Review the conversation context. Extract only durable learnings:

- commands or workflows that worked
- architecture facts or repo structure discovered
- decisions made and constraints clarified
- conventions, gotchas, or recurring pitfalls
- durable open questions worth preserving

Ignore transient chatter, one-off dead ends, and speculative guesses that were never validated.

Distinguish: **facts** (supported by code, files, commands, or explicit user confirmation), **inferred conventions** (likely patterns not fully confirmed), **open questions** (unresolved items for future sessions).

### Find the wiki

Look for files such as:

- `wiki/SCHEMA.md`
- `wiki/index.md`
- `wiki/log.md`
- `wiki/overview.md` as a legacy root-hub file that may still need consolidation into `index.md`

If the workspace uses a different but clearly established wiki root, reuse it.

If no wiki exists yet, stop and recommend:

```text
/do-wiki-build <topic or wiki goal>
```

### Check qmd readiness

1. Glob for `.wiki-metadata.json`. If found, **read it immediately**. If `retrieval.status` is `"ready"`, qmd is ready — use `retrieval.collection_name` and **skip to discovery below**. Do not run fallback checks.
2. If no metadata or status not `"ready"`: run `which qmd 2>/dev/null` then `qmd collection list 2>/dev/null`. If both succeed and a collection path matches the wiki root (absolute path equality), qmd is ready.
3. If qmd is still not ready: use Grep/Glob to find destination pages.
4. Runtime guard: if any qmd command fails or returns stale results, treat as degraded — fall back to Grep/Glob.

If qmd is ready, read `${CLAUDE_SKILL_DIR}/references/qmd-usage.md` for finding existing destination notes.

### Read the wiki contract & discover destinations

Read before proposing:

1. `<wiki root>/SCHEMA.md`
2. `<wiki root>/index.md`
3. the most recent relevant parts of `<wiki root>/log.md`
4. `<wiki root>/overview.md` when it exists and may still contain legacy root-hub context
5. the existing pages directly related to the learnings you want to capture

Resolve the scope: if `$ARGUMENTS` names a topic, use that as primary focus. Otherwise derive from the session. Use `index.md` and `Grep` to find directly related pages. Do not propose edits to a page you have not read.

**If qmd is ready**, follow `references/qmd-usage.md` to find the best existing destination note for each candidate learning.

**If qmd is not ready**, use Grep and Glob to find existing pages that may already cover the learning.

---

## Step 2 — Propose & apply

For each candidate learning, decide whether it should:

- update an existing topic, entity, concept, source, or analysis page
- create a small new durable page because no existing page is a good fit
- be preserved as an open question rather than a settled fact
- be deferred because it is not durable enough
- be routed to `/do-wiki-amend` because it reveals the wiki is wrong or stale

Rules:

1. Prefer updating existing pages over creating new ones.
2. **Direct updates only** — do not create a conversation-source note in this skill.
3. Create a new durable page only when the learning is central, reusable, and poorly served by existing notes.
4. Keep additions concise and durable. Do not dump session transcripts.
5. If a learning contradicts an existing wiki claim, surface it and recommend `/do-wiki-amend`.
6. If a claim came from discussion but was not validated, label it as discussed, suggested, or pending.

### Show the proposal (do not edit yet)

```md
## Wiki Learnings Proposal

### Summary
- Learnings reviewed: <count>
- Proposed page updates: <count>
- Proposed new pages: <count>
- Needs amend instead: <count or "none">

### Update: <page path>

**Why:** <one-line reason>

```diff
+ <concise proposed addition>
```

### New page: <page path>

**Why:** <one-line reason>

```md
# <title>
...
```

### Defer or route elsewhere
- <item>: not durable enough | should use `/do-wiki-amend` | still unresolved

### Index and log
- `index.md`: <what will change, or "unchanged">
- `log.md`: append `## [YYYY-MM-DD] learnings | <session focus>`

### Open questions
- <question or "none">
```

Then ask:

> "Does this learnings proposal look right? If yes, I'll apply it. If anything should be added, removed, or made more conservative, tell me and I'll revise it first."

Wait for explicit confirmation.

### Apply the approved learnings

1. Update the existing relevant pages.
2. Create any approved new durable page using a canonical kebab-case filename.
3. Update `index.md` if durable pages changed or discoverability improved materially.
4. Append `<wiki root>/log.md`:

```md
## [YYYY-MM-DD] learnings | <session focus>
```

Capture: session focus, pages updated or created, important learnings preserved, contradictions or items deferred, open questions left unresolved.

5. **Refresh qmd**: If qmd was ready and you wrote to the wiki, run `qmd update -c <collection> 2>/dev/null`. If refresh fails, report it but do not roll back wiki edits.

Do not create a conversation-source note in this skill.

---

## Step 3 — Report back

```md
Wiki learnings applied for <session focus>

### Touched pages
- <path>

### New pages
- <path or "none">

### Index and log
- Index: <path or "unchanged">
- Log: <path>

### Deferred or needs amend
- <item or "none">

### Open questions
- <question or "none">

### Next useful command
- `/do-wiki-add <local source path>` or `/do-wiki-amend <what changed>`
```

If the review found nothing durable enough to add, say so explicitly and do not make any edits.

---

## Rules

- Review the current session before scanning the wiki.
- Read the wiki schema before proposing edits.
- Proposal first, apply second.
- Direct page updates only. Never create a conversation-source note in this skill.
- Prefer existing pages over new pages. Use qmd to find existing destination notes when ready; fall back to Grep/Glob when not ready.
- Never edit a wiki page based only on qmd output. Always read the actual wiki files first.
- Keep additions concise, durable, and attributable to the session.
- Route corrections and supersessions to `/do-wiki-amend` instead of silently fixing them here.
- Do not fetch external sources in this skill.
- Do not modify raw-source files.
- Update `<wiki root>/log.md` on every approved learnings pass.
- After wiki writes, refresh qmd if the collection is ready. If refresh fails, report it but do not roll back.