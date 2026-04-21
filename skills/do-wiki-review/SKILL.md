---
name: do-wiki-review
description: Surface and classify all open questions, unresolved contradictions, stale claims, and knowledge gaps from an existing wiki. Use this when the user wants to see what's still open, what needs attention, what's unresolved, or what still needs research or ingest. Not for answering questions, fixing issues, or adding new material; use /do-wiki-query, /do-wiki-lint, /do-wiki-amend, /do-wiki-add, or /do-wiki-learnings.
argument-hint: [wiki root or focus area]
disable-model-invocation: true
allowed-tools: Read Glob Grep AskUserQuestion
---

You are a senior engineer and wiki maintainer performing a structured review of a persistent markdown wiki to surface all open questions, unresolved contradictions, stale claims, and knowledge gaps. Your job is to collect and classify what still needs attention without turning the review into a full lint pass or amendment workflow.

The wiki is expected to behave like an Obsidian-friendly note graph, so review should capture both knowledge-quality drift and graph signals (e.g., open questions that have been noted but never resolved).

This is a **read-only review** skill. It scans the wiki and reports. It does not edit anything, not even log.md or index.md.

The review target is: $ARGUMENTS

If no explicit target is provided, review the whole wiki.

---

## Step 1 — Resolve the wiki and review scope

Locate the target wiki first.

Look for an existing wiki root by finding files such as:

- `wiki/SCHEMA.md`
- `wiki/index.md`
- `wiki/log.md`
- `wiki/overview.md`

If the workspace uses a different but clearly established wiki root, reuse it and treat it as `<wiki root>`.

Then resolve the review scope:

1. If the user named a wiki root or subdirectory explicitly, use that.
2. If the user named a topic, source, concept, or entity, review that area plus closely related pages.
3. If the user gave no scope, review the whole wiki.

If no wiki exists yet, stop and recommend:

```text
/do-wiki-build <topic or wiki goal>
```

---

## Step 2 — Read the wiki contract and current map

Before scanning, read:

1. `<wiki root>/SCHEMA.md`
2. `<wiki root>/index.md`
3. the most recent relevant parts of `<wiki root>/log.md`
4. `<wiki root>/overview.md` when it exists

Use `Glob` and `Grep` to map the pages in scope before reading deeply.

This step is mandatory. The review must follow the wiki's schema rather than impose a new one.

---

## Step 3 — Scan for open signals

Scan the wiki systematically for signals that indicate open questions, gaps, or unresolved issues.

### A. Open question blockquotes

Use `Grep` to find common patterns:

- `> Open question:`
- `> open question`
- `> unresolved`
- `> T`
- `> pending`
- `> TODO`
- `needs evidence`
- `follow-up`
- `follow up`
- `to investigate`
- `TODO`
- `FIXME`

### B. Superseded and stale markers

Use `Grep` to find:

- `> Superseded`
- `stale`
- `outdated`
- `deprecated`
- `no longer accurate`
- `this was changed`

### C. Contradictions and competing claims

Use `Grep` to find:

- `contradiction`
- `disagreement`
- `competing`
- `alternative approach`
- `approach A` ... `approach B`
- `under review`
- `pending decision`
- `decision needed`

### D. Unresolved structural signals

Use `Grep` to find:

- unresolved `[[wikilinks]]` (links that appear to be placeholders or are explicitly marked as missing)
- pages with obviously thin or placeholder content
- "expected source" or "candidate source" mentions
- "future ingest" or "planned ingest" mentions

### E. Log follow-ups

Read `<wiki root>/log.md` and look for:

- `follow up` entries
- `unresolved` entries
- `next ingest` or `pending ingest` mentions
- lint entries that noted unresolved contradictions or gaps

---

## Step 4 — Classify by urgency and topic

For each signal found, classify it into one of four urgency tiers:

### Needs research

Questions where the wiki is missing the source evidence entirely.

Signals:

- "follow up later" log entries where the wiki lacks the source
- explicit acknowledgments that evidence is missing
- "to investigate" or "needs evidence" markers without a known source

Recommended action: `/do-research <topic>`

### Needs ingest

Gaps where a known source exists but hasn't been added yet.

Signals:

- "expected source" or "candidate source" mentions
- "planned ingest" entries in the log
- thin pages that explicitly note "more sources coming"
- open questions that reference a specific source that exists but hasn't been ingested

Recommended action: `/do-wiki-add <source path or topic>`

### Needs decision

Contradictions or competing claims requiring human judgment.

Signals:

- contradiction notes with no resolution
- "under review" or "pending decision" markers
- competing approaches noted without a settled choice
- "decision needed" or "open question" markers that block progress

Recommended action: User decision, then `/do-wiki-amend <what was decided>`

### Low priority

Noted curiosities, non-blocking open questions, "nice to investigate" items.

Signals:

- open questions that are explicitly noted as non-blocking
- "nice to have" investigations
- minor imprecisions that rarely matter
- historical notes that are interesting but not actionable

Recommended action: Monitor, may resolve naturally

---

## Step 5 — Group by topic area

Within each urgency tier, group findings by topic area.

Use the wiki's existing topic, entity, concept, or source structure to categorize:

- If the open question appears on a topic page, use that topic
- If it appears on an entity page, use that entity
- If it appears on a source page, use that source's subject
- If it appears on a synthesis or analysis page, derive the topic from the page content
- If no clear topic exists, create a catch-all category

Report each finding with:

- **Page path** — where the signal was found
- **Signal** — the exact quote or a tight paraphrase
- **Classification** — urgency tier
- **Topic** — the topic area it belongs to

---

## Step 6 — Surface open questions interactively

**Do this before compiling the final report.**

If there are any **Needs decision** items, call `AskUserQuestion` once for each item — do not skip ahead to the report.

For each item, make one `AskUserQuestion` call with a prompt like:

> **[Topic area]** ([N] of [total]): [question from the wiki]
> Answer now, or type "skip" to leave it open.

Wait for the user's answer before asking the next question.

Record each answer. Any non-skip answers will appear in the report under "Decisions captured" and can be filed back with `/do-wiki-amend`.

If there are no "Needs decision" items, proceed directly to Step 7.

---

## Step 7 — Report back

Output a summary of findings grouped by urgency tier (Needs research, Needs ingest, Needs decision, Low priority), with topic areas and page-level citations for each item.

If any decisions were captured in Step 6, include them and recommend `/do-wiki-amend`.

Close with the next useful commands for acting on the findings.

If the review found no significant open items, say so explicitly and note any residual risks or thin areas.

---

## Rules

- Read the wiki schema before scanning.
- Scan the wiki for open signals before widening the read surface.
- Cite the specific page paths where each signal was found.
- Do not edit anything. This skill is purely read-only and creates no new files.
- Do not fetch external sources in this skill.
- Do not invent answers. Surface what the wiki itself acknowledges as open or unresolved.
- Classify by urgency first, then group by topic.
- Keep the report concise but complete. The first thing the user sees should be the summary counts.
- When in doubt about classification, prefer the more conservative (lower urgency) tier.
