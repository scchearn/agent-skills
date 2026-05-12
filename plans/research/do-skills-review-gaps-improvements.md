---
name: do-skills-review-gaps-improvements
description: Whole-family review of the do-* workflow skills with gap analysis, contradiction findings, and concrete improvement suggestions. Includes mini-simulations against current SKILL.md content.
keywords:
  - do-skills
  - workflow review
  - gap analysis
  - skill consistency
  - qmd integration
  - disable-model-invocation
  - plan template
  - wiki write-back
created_at: 2026-05-12 07:30
updated_at: 2026-05-12 07:30
---

## Research summary: whole-family review of do-* skills

### Question

Audit every `do-*` skill in `skills/` for gaps, internal contradictions, cross-skill inconsistencies, and concrete improvement opportunities. Run mini-simulations of common invocation patterns to surface failure modes the current SKILL.md text does not yet cover.

### What exists today

The repo ships 14 `do-*` skills plus `agent-md-management` and `setup-obsidian-vault`. The do-* family splits cleanly into two clusters:

- **Implementation workflow:** `do-research`, `do-plan`, `do-start`, `do-amend`, `do-agents`
- **Wiki workflow:** `do-wiki-build`, `do-wiki-add`, `do-wiki-align`, `do-wiki-amend`, `do-wiki-learnings`, `do-wiki-lint`, `do-wiki-query`, `do-wiki-review`

All do-* SKILL.md files were aligned with superpowers conventions on 2026-05-07 (commit `0cdf652`): each now has a `Use when …` description, **Companion superpowers skills** section, and (on the higher-stakes ones) **Hard gates** + **Red flags** tables.

13 of 14 do-* skills carry `disable-model-invocation: true`. Plans live in `plans/<slug>.md` with a slim `plans/INDEX.md`; research memos live in `plans/research/<slug>.md` with no separate index. The wiki layer optionally accelerates discovery via a `qmd` retrieval CLI, surfaced through `.wiki-metadata.json`.

### Relevant evidence

- `skills/do-plan/SKILL.md` — the central plan-authoring skill; defines YAML front matter, INDEX schema, research-memo discovery, persisted-data rule
- `skills/do-plan/references/template.md` — the plan template `/do-start` and `/do-amend` rely on
- `skills/do-start/SKILL.md` — execution loop, pre-flight `[>]` amendment check, dependency check, hard 25-task session limit, hard 3-attempt verify limit
- `skills/do-amend/SKILL.md` — six-phase amendment process with mandatory user confirmation in Phase 4
- `skills/do-research/SKILL.md` — research-memo author; six steps, no qmd integration, no separate index
- `skills/do-agents/SKILL.md` — uses a different metadata schema (no `disable-model-invocation`, has `compatibility:` and nested `metadata.phase/system/outputs`); markedly different style from the rest of the family
- `skills/do-wiki-query/SKILL.md` — auto-trigger description ("applies whenever the user asks about project, codebase, architecture…") combined with `disable-model-invocation: true`
- `skills/do-wiki-add/SKILL.md`, `do-wiki-build/SKILL.md`, etc. — all six wiki SKILL.md files duplicate qmd-readiness check, wiki-write-back, and log-format scaffolding; high redundancy across the family
- `README.md` — single source of truth for the public skill catalogue and recommended workflow; currently lists 16 skills and no `/do-commit` or `/do-abandon`

### Constraints and conventions

- YAML front matter is the only authoritative plan metadata; legacy `updated_at` and `## Plan summary` should be stripped on edit
- `plans/INDEX.md` rows are status-ordered: `in-progress → pending → done → abandoned`
- Wiki writes should prefer extending existing notes; new durable notes use canonical kebab-case filenames and `[[wikilinks]]`
- `qmd` is optional everywhere; every wiki skill must degrade to Grep/Glob if qmd is unavailable
- Today's actual date in this workspace is 2026-05-12 (per `~/.claude/CLAUDE.md` `currentDate`); skills that say "use today's actual local date" assume the harness exposes a clock

### Mini-simulations & assumption tests

Each simulation walks the SKILL.md text mentally against a concrete user input and surfaces what breaks.

**Sim 1 — `/do-research how does X work` with 50 existing memos in `plans/research/`.**
do-plan Step 1 says: "List `plans/research/*.md`… Read only the front matter of each memo for discovery. Do not read every research file in full, and do not rely on or create any separate research index." With 50+ memos this is 50+ Reads just to get front matter. There is no qmd hook in `do-research` or `do-plan` for this scan, even though the wiki cluster uses qmd happily. **Gap.**

**Sim 2 — `/do-start plans/foo.md T9` where T9 has unmet dep T7 outside the target set.**
The pre-flight dependency check correctly STOPs and asks (a/b/c). Works. ✅

**Sim 3 — `/do-start plans/foo.md` where verify fails 4 times because of a real environment issue.**
Hard rule: "Maximum 3 attempts" → mark `[!]` and stop. But `superpowers:systematic-debugging` (companion) is a longer loop. The "3 attempts" rule conflicts with the companion's discipline. **Contradiction worth softening** — e.g., "3 surface-level retries; if hypothesis-driven debugging is in play, instrument and try again rather than giving up."

**Sim 4 — `/do-amend plans/foo.md` where the user wants to abandon the plan entirely.**
do-amend has no abandon path. INDEX.md has an `abandoned` status column but no skill writes it. User must edit YAML manually. **Gap — needs `/do-abandon` or an explicit "abandon" branch in `/do-amend`.**

**Sim 5 — `/do-start plans/foo.md` finishes T1-T8 successfully.**
do-start says "Do not commit during the execution loop. Committing is handled separately." But there is no `/do-commit` skill in the family. User has to drop into git manually with no guidance on commit-per-task vs. commit-per-plan. **Gap.**

**Sim 6 — User asks "what does this billing system do?" with no slash command.**
do-wiki-query's description claims it "applies whenever the user asks about project, codebase, architecture, workflow, decisions, or current state and the wiki likely contains the answer, even if they do not mention the wiki by name." But `disable-model-invocation: true` prevents the model from auto-invoking. **Direct contradiction in front matter.**

**Sim 7 — `/do-plan` creates a research memo via mistake, slug collides with existing memo.**
Step 4 of do-research says "If covers same question, update; if covers different question, choose more specific slug." No threshold or example. Two equally vague topic memos can collide silently. **Vagueness gap.**

**Sim 8 — Amendment supersedes T3 with `[!]` and adds T9 as the replacement.**
Phase 5/C of do-amend allows this, but does not say to update `Depends on` of downstream tasks that pointed at T3 to point at T9. Phase 5/E ("Update downstream dependencies") only covers *new* tasks becoming prerequisites. **Edge case the SKILL.md text underspecifies.**

**Sim 9 — `/do-agents` invocation alongside `/do-plan` for same task.**
do-agents emits `agents/<slug>.toml` and `agents/<slug>.md`. README workflow shows `/do-agents` slot between `/do-plan` and `/do-start`. But `do-start` knows nothing about agent configs and can't auto-launch them. The hand-off is unwritten. Also, do-agents uses different front-matter shape (no `disable-model-invocation`, has `compatibility:`, nested `metadata.*`) — agents do not auto-discover it consistently. **Schema drift.**

### Findings (consolidated)

#### Front matter / metadata

- **F1.** `do-agents` schema differs from the rest of the family (no `disable-model-invocation`, has `compatibility:` and nested `metadata.phase/system/outputs`). Either align it or document why it diverges.
- **F2.** `do-wiki-query` description is auto-trigger phrased but `disable-model-invocation: true`. Pick one. If auto-trigger is desired, drop `disable-model-invocation`. If not, soften the description to user-invoked language.
- **F3.** No `argument-hint` consistency check across the family — `do-amend` puts the description in conversation context above the prompt, which is fragile when invoked from non-Claude harnesses.

#### Workflow gaps

- **F4.** No `/do-commit` skill, despite `/do-start` explicitly handing off committing. README workflow ends at `/do-amend` with no commit path.
- **F5.** No `/do-abandon` (or `/do-amend --abandon`) even though `plans/INDEX.md` reserves an `abandoned` status.
- **F6.** No retrieval acceleration (qmd or otherwise) for `plans/research/` discovery in `do-plan` Step 1, even though the wiki cluster uses qmd. Front-matter-only Read still scales linearly.
- **F7.** No mechanism for one amendment to span multiple in-flight plans — `/do-amend` takes a single `plans/<slug>.md` argument.
- **F8.** No `/do-agents` ↔ `/do-start` hand-off — emitted `agents/<slug>.toml` is dead unless the user manually wires hcom.

#### Internal rule conflicts

- **F9.** `do-start` "Maximum 3 attempts" conflicts with the spirit of `superpowers:systematic-debugging`. Soften to "3 surface retries before switching to hypothesis-driven debugging" or similar.
- **F10.** `do-start` 25-task session cap is hard-coded with no override even when the user wants to push longer. Make it a soft limit with explicit user opt-in.
- **F11.** `do-amend` Phase 5/E does not handle the case where a `[!]` superseded task had downstream dependents — those `Depends on:` lines are never auto-rewritten.

#### Underspecified edges

- **F12.** Research-memo collision rule ("clearly covers the same question") has no threshold or worked example.
- **F13.** "Use today's actual local date" appears in many places with no shared mechanism. The harness exposes the date via system prompt context, but skills don't tell the agent where to read it. Consider a single shared snippet or an explicit `date` Bash call.
- **F14.** `description` field is bounded at 70 tokens, but the `plans/INDEX.md` table cell width is unbounded — long descriptions break narrow viewers. Consider 12–18 word cap for the index cell specifically.

#### Code/style redundancy

- **F15.** Six wiki SKILL.md files duplicate the qmd readiness check, write-back rules, and log-format scaffolding nearly verbatim. Extract into shared `references/qmd-readiness.md` and `references/wiki-write-back.md` and reference from each SKILL.md.
- **F16.** `do-research`, `do-plan`, `do-start`, `do-amend` each repeat "Optional wiki context" boilerplate (`SCHEMA.md`, `index.md`, `log.md` lookup + trust-current-repo-state caveat). Same extraction opportunity.

#### Discoverability

- **F17.** README "Common Examples" lists `/do-amend` workflow but does not show the abandon flow, the targeted-run examples (`T3`, `T3,T5`, `T3-T7`) are present but the dependency-warning UX is not surfaced anywhere user-facing.
- **F18.** No quick reference for what `[ ] [~] [x] [!] [>]` mean in user-facing docs. Defined inside the plan template only.

### Recommended next step

Convert these findings into a small, ordered improvement plan. Priority order suggested:

1. **F2, F1, F9** — fix outright contradictions (one-line front-matter / wording fixes).
2. **F4, F5** — add `/do-commit` and `/do-abandon` (or amend-mode flag) to close the workflow loop.
3. **F11, F8** — close the genuine execution-time gaps.
4. **F15, F16** — extract shared references; cuts duplication and drift risk.
5. **F6, F12, F13, F14, F17, F18** — polish.

### Suggested command

- `/do-plan tighten do-* skill consistency, add /do-commit + /do-abandon, extract shared wiki and qmd references`

### Open questions

- Should `do-agents` be brought into the standard `do-*` schema, or is its hcom-specific shape intentional and worth documenting as a deliberate exception?
- Is `disable-model-invocation: true` actually desired on `do-wiki-query` (treat as user-invoked only) or should it be removed so the model auto-routes wiki-answerable questions there?
- Does the user want a `plans/research/INDEX.md` (mirror of `plans/INDEX.md`) once the research corpus grows past N memos, or is the qmd-style retrieval the better long-term path?
- Should commit policy live in a new `/do-commit` skill, or as an opt-in step inside `/do-start`?
