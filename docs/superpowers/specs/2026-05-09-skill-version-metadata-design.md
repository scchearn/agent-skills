# Skill Version Metadata Design

**Goal:** Add per-skill semver version and author metadata to every SKILL.md frontmatter, using the agentskills.io spec-compliant `metadata` field.

**Architecture:** Add a `metadata` block with `version` (semver string) and `author` keys to each SKILL.md YAML frontmatter. No changes to index.json or tooling. All fields are spec-compliant and pass `skills-ref validate`.

**Tech Stack:** YAML frontmatter, agentskills.io spec v1

---

## Background

The agentskills.io specification defines an optional `metadata` field in SKILL.md frontmatter as "a map from string keys to string values." The spec example already shows `version: "1.0"` under `metadata`. None of our 16 skills currently use this field.

Separately, `index.json` has a top-level `version: "0.1.0"` for the bundle, but individual skills have no version tracking.

## Design

### What we're adding

Every SKILL.md gets a `metadata` block with two string keys:

```yaml
metadata:
  version: "1.0.0"
  author: "scchearn"
```

- `version`: Semver string (e.g., `"1.0.0"`, `"0.1.0"`)
- `author`: String identifying the skill author/maintainer

### Initial version assignments

| Skill | Version | Author | Rationale |
|-------|---------|--------|-----------|
| `do-amend` | `"1.0.0"` | scchearn | Core workflow skill, stable |
| `do-plan` | `"1.0.0"` | scchearn | Core workflow skill, stable |
| `do-start` | `"1.0.0"` | scchearn | Core workflow skill, stable |
| `do-agents` | `"1.0.0"` | scchearn | Core workflow skill, stable |
| `do-research` | `"1.0.0"` | scchearn | Core workflow skill, stable |
| `do-wiki-build` | `"1.0.0"` | scchearn | Wiki suite, functional |
| `do-wiki-align` | `"1.0.0"` | scchearn | Wiki suite, functional |
| `do-wiki-add` | `"1.0.0"` | scchearn | Wiki suite, functional |
| `do-wiki-learnings` | `"1.0.0"` | scchearn | Wiki suite, functional |
| `do-wiki-lint` | `"1.0.0"` | scchearn | Wiki suite, functional |
| `do-wiki-amend` | `"1.0.0"` | scchearn | Wiki suite, functional |
| `do-wiki-review` | `"1.0.0"` | scchearn | Wiki suite, functional |
| `do-wiki-query` | `"1.0.0"` | scchearn | Wiki suite, functional |
| `setup-obsidian-vault` | `"1.0.0"` | scchearn | Functional, rarely changes |
| `agent-md-improver` | `"0.1.0"` | scchearn | Still evolving |
| `revise-agent-md` | `"0.1.0"` | scchearn | Still evolving |

### What stays unchanged

- `index.json` — no schema changes; versions live only in SKILL.md
- `skill-link.ts` — no changes; it doesn't surface versions
- Existing non-standard frontmatter fields (`argument-hint`, `disable-model-invocation`, `tools`, `allowed-tools`) — kept as-is
- The body of each SKILL.md — no changes

### Spec compliance

The `metadata` field is in the spec's `ALLOWED_FIELDS` set. After adding metadata blocks, all SKILL.md files should pass `skills-ref validate`.

### Future extensibility

The `metadata` block can grow organically. Potential future keys:
- `last-updated` — date string
- `tags` — categorization
- `requires` — skill dependencies

No spec changes needed to add any of these later.

## File changes per skill

For each of the 16 skills, insert a `metadata` block into the YAML frontmatter after the last existing field (before the closing `---`). The insertion point varies by skill because frontmatter fields differ.

Example for `do-plan` (before):
```yaml
---
name: do-plan
description: Use when a workspace task, feature, or bugfix needs an execution-ready implementation plan with ordered, verifiable steps grounded in current repo context, relevant persisted research, spec files, and optional wiki notes before any code is written.
argument-hint: <feature description>
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, Write, Edit, Skill
---
```

Example for `do-plan` (after):
```yaml
---
name: do-plan
description: Use when a workspace task, feature, or bugfix needs an execution-ready implementation plan with ordered, verifiable steps grounded in current repo context, relevant persisted research, spec files, and optional wiki notes before any code is written.
argument-hint: <feature description>
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, Write, Edit, Skill
metadata:
  version: "1.0.0"
  author: scchearn
---
```