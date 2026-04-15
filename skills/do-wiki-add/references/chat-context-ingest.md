# Chat-Context Ingest Guide

Use this guide when `/do-wiki-add` is running in **chat-context mode** — the source is the current conversation, not a local file.

## When chat-context mode activates

Chat-context mode activates when the `$ARGUMENTS` value does not resolve to an existing local file and instead looks like a topic, question, or natural-language summary.

Examples of chat-context arguments:

- `why we chose SQLite over Postgres for caching`
- `discussion about API versioning strategy`
- `authentication flow decisions from today`

## How to synthesize from conversation

1. Scan the conversation for factual statements, design decisions, technical findings, and open questions.
2. Group related points under the topic or theme given in `$ARGUMENTS`.
3. Distinguish between:
   - **Decisions** — choices that were explicitly agreed upon
   - **Findings** — facts discovered during the conversation (e.g., from reading code, running commands)
   - **Preferences** — stated but not yet validated or tested
   - **Open questions** — raised but not resolved
4. Produce a concise summary that captures the durable, reusable knowledge. Skip transient chatter, greetings, and procedural steps that don't encode lasting information.
5. The summary becomes the "source" that flows into the standard add pipeline (source page, related pages, index, log).

## Provenance and uncertainty

Conversation-sourced content has different authority than file-backed content:

- Conversation claims are **discussed** or **agreed in conversation**, not **confirmed by source material**.
- Prefer phrasing like "X was discussed as the preferred approach" over "X is the approach."
- If a point was mentioned once without pushback but also without explicit agreement, mark it as "raised" or "suggested" rather than "decided."
- If code was read or commands were run during the conversation and produced verifiable output, those findings can carry higher confidence. Note the evidence source when feasible (e.g., "from reading `src/auth/middleware.ts`").

## Source page template adjustments

When `Source type: conversation`, adjust the source summary template:

- Replace `Source path:` with `Source: conversation context`
- Add `Conversation date: YYYY-MM-DD`
- Optionally add `Participants: <who was in the chat>` if discernible; omit if unclear
- Keep `Ingested:` and `Status:` fields as normal
- Under `Key points`, prefer phrasing that attributes claims to the discussion rather than presenting them as established facts

Example:

```md
# Why We Chse SQLite Over Postgres for Caching

- Source: conversation context
- Source type: conversation
- Conversation date: 2026-04-15
- Ingested: 2026-04-15
- Status: active

## Summary

During the session, SQLite was discussed as the preferred caching store over Postgres. The primary reasons were simpler deployment, no network dependency for local dev, and sufficient read throughput for the expected cache workload.

## Key points

- Decision: SQLite chosen for caching layer (agreed in conversation)
- Postgres remains the primary data store; cache is a separate concern
- Write-heavy cache scenarios were flagged as an open question

## Open questions

- How does SQLite perform under high write contention in the cache layer?
- Should cache invalidation use TTL or explicit triggers?

## Related pages

- [[caching-strategy]]
- [[sqlite]]
```

## Quality threshold

- Conversation context is inherently less verified than a curated document. The wiki should reflect this honestly.
- If the conversation only touched a topic superficially, prefer a short source note with clear open questions over a detailed page that overstates what was discussed.
- It is better to add a stub with explicit uncertainty than to fabricate detail the conversation did not produce.