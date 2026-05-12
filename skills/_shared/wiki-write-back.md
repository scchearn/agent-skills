# Shared reference — wiki write-back rules

This snippet is referenced by every `do-*` skill that may file durable findings back into a workspace wiki. It defines what to write back, how to write it, and how to keep the wiki graph connected. If no wiki exists in the workspace, write-back does not apply.

## When to write back

Good candidates:

- stable architecture facts verified during the work
- durable integration constraints or behavior
- recurring debugging discoveries or operational gotchas
- clarified domain rules, taxonomies, or reusable comparisons
- non-trivial cross-source synthesis that future sessions will reuse
- when the user explicitly asked to save, file, preserve, or turn it into a page

## When not to write back

- raw task progress notes
- temporary dead ends or speculative scope notes
- the task list itself or ephemeral plan-management chatter
- narrow, operationally trivial lookups
- anything that would create an isolated page with weak graph connections — when in doubt, prefer fewer new pages

## How to write back

1. Prefer **updating an existing** relevant topic, concept, entity, source, or analysis note over creating a new one.
2. If you must create a new durable category note:
   - Use a canonical kebab-case filename.
   - Use `[[kebab-case-note-name]]` links to connect it into the surrounding graph.
   - Include a `Related pages` section with at least one `[[wikilink]]` to a hub or related note.
3. Update `index.md` if durable pages changed.
4. Append a parseable entry to `<wiki root>/log.md`:

   ```md
   ## [YYYY-MM-DD] <event> | <scope or feature>
   ```

   Use the event tag that matches the calling skill (`research`, `plan`, `execution`, `amend`, `query`, `learnings`, `align`, `lint`, `review`, `add`, `build`, etc.).

## Refresh qmd after writes

If qmd was ready when the write happened, run `qmd update -c <collection> 2>/dev/null` to reindex. If refresh fails, report it but do not roll back successful wiki edits. See `qmd-readiness.md`.

## Trust order

If current repo state, tests, or primary docs conflict with the wiki: trust the current repo state. Update the wiki only if the correction is durable.
