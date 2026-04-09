# Analysis Template

Use this template when a query result deserves a durable page in the wiki.

Suggested path:

```text
<wiki root>/analyses/<analysis-slug>.md
```

The filename should be kebab-case. The H1 title inside the file can remain human-readable.

Suggested structure:

```md
# <Analysis Title>

- Query: <original user question>
- Created: YYYY-MM-DD
- Scope: <what this analysis covers>

## Short answer

<2-5 sentence answer>

## Evidence from the wiki

- [[note-name]] — <why it matters>
- [[another-note-name]] — <why it matters>

## Synthesis

<combined explanation or comparison>

## Caveats and uncertainty

- ...

## Related pages

- [[overview]]
- [[index]]
- [[related-page-slug]]
```

Notes:

- Prefer `[[kebab-case-note-name]]` for durable internal references.
- If the wiki already uses a different schema for analysis pages, adapt to that instead of forcing this exact outline.
- Keep the short answer near the top so future sessions can scan quickly.
- Avoid isolated analysis pages. When an analysis materially informs an existing topic, entity, or concept note, update that note to link back to the analysis where appropriate.
