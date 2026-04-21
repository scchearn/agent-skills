---
name: do-wiki-learnings
description: Review the current session for durable learnings that belong in an existing Obsidian-friendly markdown wiki. Use this when the conversation uncovered decisions, architecture facts, commands, conventions, gotchas, or open questions that future sessions should inherit, and you want a proposal-first report before editing the wiki. Not for source ingestion or correcting stale wiki claims; use /do-wiki-add or /do-wiki-amend.
argument-hint: [topic or session summary]
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, Write, Edit
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

## Step 1 — Reflect on the session

Review the conversation context before touching the wiki.

Extract only durable learnings such as:

- commands or workflows that worked
- architecture facts or repo structure discovered during the session
- decisions made and constraints clarified
- conventions, gotchas, or recurring pitfalls
- durable open questions worth preserving for later follow-up

Ignore transient chatter, one-off dead ends, and speculative guesses that were never validated.

Distinguish clearly between:

- **facts** — supported by code, files, commands, or explicit user confirmation in the session
- **inferred conventions** — likely patterns suggested by the session but not fully confirmed
- **open questions** — unresolved items that future sessions should see

---

## Step 2 — Find the wiki and the relevant scope

Locate the existing wiki first.

Look for files such as:

- `wiki/SCHEMA.md`
- `wiki/index.md`
- `wiki/log.md`
- `wiki/overview.md`

If the workspace uses a different but clearly established wiki root, reuse it.

If no wiki exists yet, stop and recommend:

```text
/do-wiki-build <topic or wiki goal>
```

Then resolve the scope for this learnings pass:

1. If `$ARGUMENTS` names a topic, area, or page, use that as the primary focus.
2. Otherwise derive the most relevant focus from the session itself.
3. Use `index.md`, `overview.md`, and `Grep` to find the pages most directly related to those learnings.
4. Do not propose edits to a page you have not read.

---

## Step 3 — Read the wiki contract before proposing changes

Before drafting any additions, read:

1. `<wiki root>/SCHEMA.md`
2. `<wiki root>/index.md`
3. the most recent relevant parts of `<wiki root>/log.md`
4. `<wiki root>/overview.md` when it exists
5. the existing pages directly related to the learnings you want to capture

This step is mandatory. The goal is to update the existing note graph consistently, not improvise page-by-page.

---

## Step 4 — Draft the learnings additions

Build a targeted proposal from the session learnings.

For each candidate learning, decide whether it should:

- update an existing topic, entity, concept, source, or analysis page
- create a small new durable page because no existing page is a good fit
- be preserved as an open question rather than a settled fact
- be deferred because it is not durable enough
- be routed to `/do-wiki-amend` because it reveals the wiki is wrong or stale rather than merely missing context

Use these rules:

1. Prefer updating existing pages over creating new ones.
2. **Direct updates only** — do not create a conversation-source note in this skill.
3. Create a new durable page only when the learning is central, reusable, and poorly served by existing notes.
4. Keep additions concise and durable. Do not dump session transcripts into the wiki.
5. If a learning contradicts an existing wiki claim, do not silently fix it here. Surface it and recommend `/do-wiki-amend` unless the user explicitly wants that correction folded into the same follow-up.
6. If a claim came from discussion but was not validated, label it as discussed, suggested, or pending instead of settled.

---

## Step 5 — Show the proposed changes

**Do not edit anything yet.**

Output the proposal before making any updates.

Use this format:

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

After presenting the proposal, ask:

> "Does this learnings proposal look right? If yes, I'll apply it. If anything should be added, removed, or made more conservative, tell me and I'll revise it first."

Wait for explicit confirmation before editing files.

---

## Step 6 — Apply the approved learnings

After the user confirms, apply the smallest correct set of approved changes.

When applying:

1. Update the existing relevant pages.
2. Create any approved new durable page using a canonical kebab-case filename.
3. Update `index.md` if durable pages changed or if discoverability improved materially.
4. Append `<wiki root>/log.md` with a parseable heading:

```md
## [YYYY-MM-DD] learnings | <session focus>
```

The log entry should capture:

- the session focus
- pages updated or created
- important learnings preserved
- contradictions or items deferred to `/do-wiki-amend`
- any open questions left unresolved

Do not create a conversation-source note in this skill.

---

## Step 7 — Report back

After applying the approved changes, output:

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
- Prefer existing pages over new pages.
- Keep additions concise, durable, and attributable to the session.
- Route corrections and supersessions to `/do-wiki-amend` instead of silently fixing them here.
- Do not fetch external sources in this skill.
- Do not modify raw-source files.
- Update `<wiki root>/log.md` on every approved learnings pass.
