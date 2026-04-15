# Source Summary Template

Adapt this template to the wiki's existing schema and naming conventions.

Suggested path:

```text
wiki/sources/<source-slug>.md
```

The filename should be kebab-case. The H1 title inside the file can remain human-readable.

Suggested structure:

```md
# <Source Title>

- Source path: `<local/path/to/source>` (file mode) or `Source: conversation context` (chat-context mode)
- Source type: <article | note | transcript | report | pdf | dataset | conversation | other>
- Conversation date: YYYY-MM-DD (chat-context mode only; omit in file mode)
- Ingested: YYYY-MM-DD
- Status: active

## Summary

<2-6 sentence summary of the source>

## Key points

- ...

## Entities

- [[entity-slug]] — <why this entity matters in the source>

## Concepts

- [[concept-slug]] — <why this concept matters in the source>

## Related topics

- [[topic-slug]] — <how the source contributes>

## Contradictions or updates

- <where this source challenges, narrows, or strengthens an existing wiki claim>

## Open questions

- ...

## Related pages

- [[overview]]
- [[index]]
- [[related-page-slug]]
```

Notes:

- Keep the source path explicit. In chat-context mode, use `Source: conversation context` and include `Conversation date`.
- Prefer links to repeated prose.
- Use `[[kebab-case-note-name]]` for durable internal links.
- If the source is narrow, keep the page short.
- Every wikilink should resolve to an existing canonical note or to a note created or updated in the same ingest pass.
- Key entity, topic, and concept notes should link back to this source note under `Sources`, `Mentioned in`, or `Related pages` when the relationship is materially useful.
- If the wiki already uses different heading names, follow the existing schema instead of forcing this exact outline.
