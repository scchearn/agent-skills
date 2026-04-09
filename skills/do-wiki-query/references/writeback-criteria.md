# Query Write-Back Criteria

Use this guide to decide whether a query result should become a durable wiki page.

## Write back

Write the result back when it has clear reuse value beyond the current chat.

Strong signals:

- the answer compares multiple topics, entities, or sources
- the answer synthesizes information scattered across several pages
- the answer creates a useful framework, taxonomy, or summary of the wiki
- the answer resolves a recurring or likely-recurring question
- the user explicitly asks to save it, file it, preserve it, or turn it into a page

Typical destinations:

- `<wiki root>/analyses/` for a new analysis page
- an existing topic, concept, or entity page when the answer obviously belongs there
- a hub note such as `overview.md` or `index.md` when the analysis materially improves navigation

## Maybe write back

Use judgment when:

- the answer is moderately useful but narrow
- the answer mostly restates one page with a little added framing
- the wiki already has an analysis page that only needs a small refresh

In these cases, prefer updating an existing page over creating a new one.

## Do not write back

Do not create a page when:

- the answer is a simple lookup from one page
- the answer is temporary, chat-specific, or operationally trivial
- the wiki does not yet have enough evidence to support a durable synthesis
- writing a page would create clutter rather than improve reuse
- the result would become an isolated page with weak graph connections

## If writing back

- update `index.md`
- append a query entry to `<wiki root>/log.md`
- keep citations to the supporting wiki pages
- add `Related pages` wikilinks in the written page
- update at least one clearly relevant existing note or hub note when that relationship is materially useful
- prefer one clear page over several tiny pages
