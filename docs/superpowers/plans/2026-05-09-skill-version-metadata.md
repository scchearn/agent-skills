# Skill Version Metadata Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add `metadata.version` and `metadata.author` to every SKILL.md frontmatter in the agent-skills repo.

**Architecture:** Edit YAML frontmatter in 16 SKILL.md files to include a `metadata` block with semver `version` and `author` keys. One skill (`do-agents`) already has a `metadata` block — merge the new keys into it. All others get a new `metadata` block inserted after their last existing frontmatter field.

**Tech Stack:** YAML frontmatter, agentskills.io spec

---

### Task 1: Add metadata to core workflow skills (do-amend, do-plan, do-start, do-research)

**Files:**
- Modify: `skills/do-amend/SKILL.md`
- Modify: `skills/do-plan/SKILL.md`
- Modify: `skills/do-start/SKILL.md`
- Modify: `skills/do-research/SKILL.md`

- [ ] **Step 1: Add metadata to do-amend/SKILL.md**

Insert after `allowed-tools: Read, Write, Edit, Glob, Grep`:

```yaml
metadata:
  version: "1.0.0"
  author: scchearn
```

- [ ] **Step 2: Add metadata to do-plan/SKILL.md**

Insert after `allowed-tools: Read, Glob, Grep, Write, Edit, Skill`:

```yaml
metadata:
  version: "1.0.0"
  author: scchearn
```

- [ ] **Step 3: Add metadata to do-start/SKILL.md**

Insert after `allowed-tools: Read, Write, Edit, Glob, Grep, Bash`:

```yaml
metadata:
  version: "1.0.0"
  author: scchearn
```

- [ ] **Step 4: Add metadata to do-research/SKILL.md**

Insert after `allowed-tools: Read, Glob, Grep, Bash, WebFetch, Write, Edit`:

```yaml
metadata:
  version: "1.0.0"
  author: scchearn
```

- [ ] **Step 5: Commit core workflow skills**

```bash
git add skills/do-amend/SKILL.md skills/do-plan/SKILL.md skills/do-start/SKILL.md skills/do-research/SKILL.md
git commit -m "feat: add version and author metadata to core workflow skills"
```

---

### Task 2: Add metadata to do-agents (merge into existing metadata block)

**Files:**
- Modify: `skills/do-agents/SKILL.md`

- [ ] **Step 1: Merge version and author into existing metadata block**

The current frontmatter has:

```yaml
metadata:
  phase: planning
  system: hcom
  outputs: agents-slug-artifacts-or-loader
```

Change to:

```yaml
metadata:
  version: "1.0.0"
  author: scchearn
  phase: planning
  system: hcom
  outputs: agents-slug-artifacts-or-loader
```

- [ ] **Step 2: Commit do-agents**

```bash
git add skills/do-agents/SKILL.md
git commit -m "feat: add version and author metadata to do-agents skill"
```

---

### Task 3: Add metadata to wiki skills (9 files)

**Files:**
- Modify: `skills/do-wiki-build/SKILL.md`
- Modify: `skills/do-wiki-align/SKILL.md`
- Modify: `skills/do-wiki-add/SKILL.md`
- Modify: `skills/do-wiki-learnings/SKILL.md`
- Modify: `skills/do-wiki-lint/SKILL.md`
- Modify: `skills/do-wiki-amend/SKILL.md`
- Modify: `skills/do-wiki-review/SKILL.md`
- Modify: `skills/do-wiki-query/SKILL.md`

- [ ] **Step 1: Add metadata to do-wiki-build/SKILL.md**

Insert after `allowed-tools: Read, Glob, Grep, Write, Edit, AskUserQuestion, Skill, Bash`:

```yaml
metadata:
  version: "1.0.0"
  author: scchearn
```

- [ ] **Step 2: Add metadata to do-wiki-align/SKILL.md**

Insert after `allowed-tools: Read, Glob, Grep, Write, Edit, Bash`:

```yaml
metadata:
  version: "1.0.0"
  author: scchearn
```

- [ ] **Step 3: Add metadata to do-wiki-add/SKILL.md**

Insert after `allowed-tools: Read, Glob, Grep, Write, Edit, Bash`:

```yaml
metadata:
  version: "1.0.0"
  author: scchearn
```

- [ ] **Step 4: Add metadata to do-wiki-learnings/SKILL.md**

Insert after `allowed-tools: Read, Glob, Grep, Write, Edit, Bash`:

```yaml
metadata:
  version: "1.0.0"
  author: scchearn
```

- [ ] **Step 5: Add metadata to do-wiki-lint/SKILL.md**

Insert after `allowed-tools: Read, Glob, Grep, Write, Edit, Bash`:

```yaml
metadata:
  version: "1.0.0"
  author: scchearn
```

- [ ] **Step 6: Add metadata to do-wiki-amend/SKILL.md**

Insert after `allowed-tools: Read Glob Grep Write Edit Bash`:

```yaml
metadata:
  version: "1.0.0"
  author: scchearn
```

- [ ] **Step 7: Add metadata to do-wiki-review/SKILL.md**

Insert after `allowed-tools: Read Glob Grep AskUserQuestion Bash`:

```yaml
metadata:
  version: "1.0.0"
  author: scchearn
```

- [ ] **Step 8: Add metadata to do-wiki-query/SKILL.md**

Insert after `allowed-tools: Read, Glob, Grep, Write, Edit, Bash`:

```yaml
metadata:
  version: "1.0.0"
  author: scchearn
```

- [ ] **Step 9: Commit wiki skills**

```bash
git add skills/do-wiki-build/SKILL.md skills/do-wiki-align/SKILL.md skills/do-wiki-add/SKILL.md skills/do-wiki-learnings/SKILL.md skills/do-wiki-lint/SKILL.md skills/do-wiki-amend/SKILL.md skills/do-wiki-review/SKILL.md skills/do-wiki-query/SKILL.md
git commit -m "feat: add version and author metadata to wiki skills"
```

---

### Task 4: Add metadata to remaining skills (setup-obsidian-vault, agent-md-improver, revise-agent-md)

**Files:**
- Modify: `skills/setup-obsidian-vault/SKILL.md`
- Modify: `skills/agent-md-management/skills/agent-md-improver/SKILL.md`
- Modify: `skills/agent-md-management/skills/revise-agent-md/SKILL.md`

- [ ] **Step 1: Add metadata to setup-obsidian-vault/SKILL.md**

Insert after `allowed-tools: Bash`:

```yaml
metadata:
  version: "1.0.0"
  author: scchearn
```

- [ ] **Step 2: Add metadata to agent-md-improver/SKILL.md**

This skill uses `tools:` instead of `allowed-tools:`. Insert after `tools: Read, Glob, Grep, Bash, Edit`:

```yaml
metadata:
  version: "0.1.0"
  author: scchearn
```

- [ ] **Step 3: Add metadata to revise-agent-md/SKILL.md**

Insert after `tools: Read, Glob, Grep, Edit`:

```yaml
metadata:
  version: "0.1.0"
  author: scchearn
```

- [ ] **Step 4: Commit remaining skills**

```bash
git add skills/setup-obsidian-vault/SKILL.md skills/agent-md-management/skills/agent-md-improver/SKILL.md skills/agent-md-management/skills/revise-agent-md/SKILL.md
git commit -m "feat: add version and author metadata to setup-obsidian-vault and agent-md-management skills"
```

---

### Task 5: Validate all SKILL.md files parse correctly

**Files:** (read-only verification)

- [ ] **Step 1: Verify YAML frontmatter parses for every skill**

```bash
for f in skills/do-amend/SKILL.md skills/do-plan/SKILL.md skills/do-start/SKILL.md skills/do-agents/SKILL.md skills/do-research/SKILL.md skills/do-wiki-build/SKILL.md skills/do-wiki-align/SKILL.md skills/do-wiki-add/SKILL.md skills/do-wiki-learnings/SKILL.md skills/do-wiki-lint/SKILL.md skills/do-wiki-amend/SKILL.md skills/do-wiki-review/SKILL.md skills/do-wiki-query/SKILL.md skills/setup-obsidian-vault/SKILL.md skills/agent-md-management/skills/agent-md-improver/SKILL.md skills/agent-md-management/skills/revise-agent-md/SKILL.md; do echo "=== $f ==="; python3 -c "import yaml; content=open('$f').read(); parts=content.split('---',2); data=yaml.safe_load(parts[1]); print('name:', data.get('name')); print('metadata:', data.get('metadata'))"; done
```

Expected: Every skill shows its `name` and a `metadata` dict with `version` and `author` keys.

- [ ] **Step 2: Verify no YAML syntax errors**

```bash
for f in skills/do-*/SKILL.md skills/setup-obsidian-vault/SKILL.md skills/agent-md-management/skills/*/SKILL.md; do python3 -c "import yaml; content=open('$f').read(); parts=content.split('---',2); yaml.safe_load(parts[1])" && echo "OK: $f" || echo "FAIL: $f"; done
```

Expected: All files print "OK".

