# Shared reference — qmd readiness and fallback

This snippet is referenced by every wiki skill that may use qmd for retrieval acceleration. qmd is always **optional**. If it is not ready, fall back to Grep and Glob without breaking.

## Readiness check

1. Glob for `.wiki-metadata.json`. If found, read it immediately.
   - If `retrieval.status` is `"ready"`, qmd is ready. Use `retrieval.collection_name` and go straight to qmd search.
   - Do not read `SCHEMA.md`, `index.md`, or run fallback checks when metadata says `"ready"`.
2. If no metadata or status is not `"ready"`:
   - Run `which qmd 2>/dev/null` then `qmd collection list 2>/dev/null`.
   - If both succeed and a collection path matches the wiki root by absolute path equality, qmd is ready.
3. If qmd is still not ready, use Grep/Glob search.
4. **Runtime guard:** if any qmd command fails or returns stale results, treat as degraded and fall back to Grep/Glob.

## qmd search patterns

Use `--files` to get candidate file paths only (no snippets). Then Read the actual wiki files to verify.

- **Lookup** (answer from one or a few pages): `qmd search "<keywords>" --files -n 8 -c <collection>`
- **Comparison / synthesis / natural-language query**: `qmd query "<question>" --files -n 8 -c <collection>`
- Strip the `qmd://<collection>/` prefix from file paths to get the relative wiki path (e.g. `entities/omf-programme-areas.md`).
- Noisy results: retry with different terms or add `intent:` to disambiguate.
- Use scores to prioritize which files to Read first.

## Verification rule (non-negotiable)

qmd discovers file paths; Read confirms content. Always verify qmd candidates by Reading the actual wiki files. If qmd results disagree with the file content, trust the file.

## Refresh after writes

If the calling skill wrote to the wiki and qmd was ready:

1. Run `qmd update -c <collection> 2>/dev/null` to reindex changed files.
2. If refresh fails, report it but do not roll back successful wiki edits.
