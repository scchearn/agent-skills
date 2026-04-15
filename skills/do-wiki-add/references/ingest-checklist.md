# Add Checklist

Use this checklist before marking an add pass complete.

## Before editing

- Confirm the target source is local and unambiguous (file mode), or confirm the conversation topic is clear (chat-context mode).
- If chat-context mode: confirm the synthesized summary is faithful to what was discussed. Flag any points that were mentioned but not confirmed.
- Confirm the wiki root and schema.
- Read `index.md`.
- Read the latest relevant log entries.
- Read related source, topic, entity, and concept pages.

## During ingest

- Create or update a source page.
  - File mode: source path points to a local file.
  - Chat-context mode: source page uses `Source type: conversation` and `Conversation date`.
- Update only the pages materially affected by the source.
- Create a new entity or concept page only when it is important enough to reuse.
- Preserve uncertainty and provenance.
  - File mode: cite the source path and distinguish facts from claims in the source.
  - Chat-context mode: mark conversation-sourced claims with appropriate uncertainty (discussed, suggested, agreed in conversation).
- Make contradictions explicit instead of silently flattening them.

## Before finishing

- Update `index.md`.
- Append `log.md`.
  - File mode: `## [YYYY-MM-DD] add (file) | <source title>`
  - Chat-context mode: `## [YYYY-MM-DD] add (chat) | <topic>`
- Check that every touched durable page is discoverable from the index or inbound links.
- Confirm raw-source files were not modified.

## Good add outcomes

- The wiki is richer than before.
- The path from source to synthesis is still visible.
- A future session can answer related questions faster by reading the wiki first.
- In chat-context mode: uncertainty is explicit, not overstated.