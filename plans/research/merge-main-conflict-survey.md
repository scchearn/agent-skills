---
name: merge-main-conflict-survey
description: Trial-merge survey of origin/main into align-with-superpowers — enumerates the 4 textual conflicts and the 4 semantic clashes that need reconciliation before this branch can be merged or PR'd.
keywords:
  - merge
  - conflict
  - origin/main
  - align-with-superpowers
  - skill metadata
  - hcom orchestration
  - semantic conflict
created_at: 2026-05-12 12:05
updated_at: 2026-05-12 12:05
---

## Research summary: merge main into current branch, look for conflict and conflicting logic

### Question

Will `git merge origin/main` into `align-with-superpowers` succeed cleanly, and if not, where are the textual and semantic conflicts? The branch has just finished an 11-task plan (`tighten-do-skill-consistency`) that materially edited every `do-*` SKILL.md. Upstream `main` is 3 commits ahead and edits an overlapping file set.

### What exists today

**Branch divergence (from `git fetch origin`, 2026-05-12):**

- Local branch `align-with-superpowers` is **6 commits ahead** of `main` (3 from this plan, 3 from prior README + alignment work).
- `origin/main` is **3 commits ahead** of local `main`:
  - `d41a969` Add hcom-aware plan execution guidance — adds `skills/do-start/references/hcom-orchestration.md` and edits `skills/do-plan/SKILL.md`, `skills/do-start/SKILL.md` to reference hcom.
  - `2be3c15` feat: add version and author metadata to all SKILL.md frontmatter — adds `metadata: { version: "1.0.0", author: scchearn }` block to every `SKILL.md` in the repo; also adds two design docs under `docs/superpowers/`.
  - `22a808b` docs: update do-plan description, add evals fixtures, update index.json and package.json — adds `package.json`, `skills/do-plan/evals/evals.json`, rewrites `do-plan`'s `description`, and updates `index.json`.

**Trial-merge outcome (`git merge --no-commit --no-ff origin/main`, then `git merge --abort`):**

```
CONFLICT (content): skills/do-amend/SKILL.md
CONFLICT (content): skills/do-plan/SKILL.md
CONFLICT (content): skills/do-start/SKILL.md
CONFLICT (content): skills/do-wiki-align/SKILL.md
```

15 other files auto-merged cleanly (README.md, index.json, the other wiki SKILL.md files, `agent-md-management/*`, `do-agents/SKILL.md`, etc.).

### Relevant evidence

- `git log --oneline main..HEAD` — lists the 6 commits this branch will push, including `305d1ae tighten-do-skill-consistency: bundle T1-T11`.
- `git log --oneline HEAD..origin/main` — lists the 3 upstream commits.
- `git merge --no-commit --no-ff origin/main` — surfaces the 4 textual conflicts above; safely aborted with `git merge --abort` for this research run.
- `git merge-tree $(git merge-base HEAD origin/main) HEAD origin/main` — offline preview; confirms the same 4 files plus several `added in remote` entries (hcom-orchestration.md, evals.json, the two superpowers docs, package.json).
- `git diff origin/main..HEAD --stat` for the 4 conflicting files:
  - `skills/do-start/SKILL.md` — **274 lines changed** (the heaviest divergence; both sides rewrote the body).
  - `skills/do-plan/SKILL.md` — 131 lines changed; remote net-grew the file, ours net-shrank it.
  - `skills/do-amend/SKILL.md` — 74 lines changed; our T4 added downstream-dep rewrite logic.
  - `skills/do-wiki-align/SKILL.md` — 24 lines changed; smaller, mostly the new metadata block + our `## Shared references` block.

### Conflict analysis

#### Textual conflicts (4 files)

Every conflicting file has at least the **front-matter conflict pattern**: upstream adds a `metadata: { version, author }` block immediately after `allowed-tools:`; our branch leaves front matter unchanged but rewrites the `description:` line. Both sides touch lines 2–11, so a 3-way merge fails.

Beyond front matter:

- **`skills/do-start/SKILL.md`** — the heaviest semantic clash. Upstream replaces the introductory paragraph with hcom-aware text ("Work autonomously, log decisions, and stop only for real blockers"). Our branch keeps the longer original phrasing and adds Companion + Hard gates + Red flags + Shared references sections, plus softens the 3-attempt verify rule into a `superpowers:systematic-debugging` escalation. **Both sides edit the same opening block and the verify/debug section.**
- **`skills/do-plan/SKILL.md`** — upstream rewrites the `description:` line and adds `Skill` to `allowed-tools:`. Our branch rewrites the same `description:` line differently and added `## Shared references`. Body sections also diverge (upstream is net longer; ours is shorter).
- **`skills/do-amend/SKILL.md`** — our T4 expanded Phase 5/E with downstream-dep rewriting; upstream added the metadata block plus minor body tweaks in adjacent lines.
- **`skills/do-wiki-align/SKILL.md`** — small overlap: metadata block vs. our Shared-references block, both inserted near the top.

#### Semantic conflicts (logic-level, not always textual)

These are clashes that `git` will not flag but that a careful reviewer must reconcile:

1. **Description rewrites.** Both branches rewrote the `description:` line on `do-plan` and `do-start`, but with different framings:
   - Ours: long, lists outputs, references `/do-amend` and `plans/INDEX.md`.
   - Upstream: short, emphasises hcom-delegated execution (`do-start`) and persisted research + spec files (`do-plan`).
   The merge must pick one, or fuse them — they cannot both win.

2. **hcom integration vs. shared-references abstraction.** Upstream added `skills/do-start/references/hcom-orchestration.md` and points `do-start` at it. Our branch added `skills/_shared/{wiki-context,qmd-readiness,wiki-write-back}.md` and points every `do-*` skill at those. Both reference-folder strategies coexist (different paths), but the do-start body now needs both `Shared references` (ours) and the new hcom orchestration pointer (upstream).

3. **Metadata schema decision.** Upstream introduces a `metadata: { version, author }` front-matter block on every SKILL.md. Our `do-agents` already has a richer `metadata: { phase, system, outputs }`. The merge must either nest both (e.g. `metadata: { version, author, phase, system, outputs }`) or deliberately drop one. Our T2 decisions log documented `do-agents`'s divergence as intentional.

4. **`do-plan` `allowed-tools` divergence.** Upstream adds `Skill` to `allowed-tools` for `do-plan` (so it can invoke other skills). Our `do-plan` leaves the list as `Read, Glob, Grep, Write, Edit`. This is a real capability change — silently keeping ours would regress upstream functionality.

#### Files added by remote (no conflict, but bring new surface)

- `skills/do-start/references/hcom-orchestration.md` — `do-start`'s body needs an updated reference link (touched by the conflict resolution).
- `skills/do-plan/evals/evals.json` — new evaluation harness for `do-plan`; brings expectations our description rewrite may break.
- `package.json` — first node manifest in repo; introduces tooling expectations.
- `docs/superpowers/plans/2026-05-09-skill-version-metadata.md` and `docs/superpowers/specs/2026-05-09-skill-version-metadata-design.md` — design rationale for the metadata block.

#### Files modified by remote that auto-merged but warrant a sanity check

- `README.md` — both sides edited it; auto-merge worked but the resulting flow should be re-read for narrative coherence.
- `index.json` — both sides added entries; verify the upstream description for `do-plan` did not regress.

### Constraints and conventions

- Trial-merge artifacts must never be left in the working tree. This research run aborted with `git merge --abort` before writing any file. (Conflict-marker contents in working files at the time of merge are not "primary evidence" — primary evidence is the conflict list itself and the per-file diff stats from `git diff origin/main..HEAD`.)
- The branch has not been pushed; rewriting history (rebase or squash) is safe.
- Our `do-agents` Schema notes section (added by T2) explicitly documents that `do-agents` carries a non-standard `metadata` block. Reconciling with upstream's `metadata: { version, author }` should preserve that note.
- `superpowers:finishing-a-development-branch` Step 1 requires tests to pass before completion. This repo has no test toolchain, so the gate is effectively "manual review of merge conflicts".

### Recommended next step

Do a deliberate, manual merge in a focused session — not autonomously. There are too many semantic decisions for a `/do-start` loop to make without user input. Recommended ordering:

1. Create a feature branch off `align-with-superpowers` named `merge-main` (so we can iterate without dirtying the work branch).
2. Run `git merge origin/main` and resolve each of the 4 conflicts with these rules:
   - **Front matter:** keep our description rewrites; also add upstream's `metadata: { version: "1.0.0", author: scchearn }` block; on `do-agents`, fuse with the existing nested metadata.
   - **do-start body:** keep our Companion + Hard gates + Red flags + Shared references + softened-verify rule; also fold in upstream's hcom-orchestration pointer; keep upstream's tighter opening paragraph if it does not contradict our hard gates.
   - **do-plan body:** keep our Shared references + reduced boilerplate; merge upstream's `Skill` addition into `allowed-tools`; reconcile descriptions by picking one (recommend upstream's shorter one) and updating `index.json` to match.
   - **do-amend body:** keep our T4 downstream-dep rewrite; accept upstream's metadata-block insertion.
   - **do-wiki-align body:** keep both blocks (Shared references + metadata).
3. After resolution: re-grep the do-* family to confirm every SKILL.md now has both `metadata:` (with `version: "1.0.0"`) and `## Shared references`, and that the `## Companion superpowers skills` section still sits above Shared references.
4. Sanity-check `index.json` and `README.md` for narrative consistency after auto-merge.
5. Optionally invoke `/do-plan` to convert the above into a fully tracked plan if the conflict resolution turns out to need >1 session.

### Suggested command

- `/do-plan reconcile origin/main merge: preserve T1-T11 changes, fold in version metadata, hcom orchestration pointer, and do-plan Skill tool` — recommended if the resolution will span multiple sessions or you want an audit trail.
- Alternatively, do it inline now: `git checkout -b merge-main && git merge origin/main`, resolve the 4 conflicts with the rules above, commit, then come back here.

### Open questions

- Is `scchearn` the canonical `author` value for skills in this repo, or should our branch's commits use a different author (e.g. `dikokb`)? Affects whether we copy the upstream metadata block verbatim.
- Should `do-agents` adopt the `version: "1.0.0", author: ...` keys in addition to its existing `phase`, `system`, `outputs` keys? The Schema notes section we added should be updated accordingly if yes.
- Does `skills/do-plan/evals/evals.json` codify expectations that our description rewrite would fail (e.g. evaluator looks for specific phrases in `description`)? Need to inspect the eval before finalising the description.
- Local `main` is behind `origin/main` by the same 3 commits. Should we `git checkout main && git pull` first, then merge, or merge `origin/main` directly into the feature branch? Either works; merging `origin/main` directly avoids a second sync step.
