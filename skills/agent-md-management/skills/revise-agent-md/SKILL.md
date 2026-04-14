---
name: revise-agent-md
description: Update agent guidance markdown files with learnings from the current session. Use when a session uncovered missing context, useful commands, testing patterns, environment quirks, or gotchas that should be captured for future agents.
tools: Read, Glob, Grep, Edit
---

# Revise Agent Markdown

Review the current session for learnings about working in this codebase. Update the relevant agent guidance markdown file with context that would help future agent sessions be more effective.

## Step 1: Reflect

What context was missing that would have helped an agent work more effectively?
- Bash commands that were used or discovered
- Code style patterns followed
- Testing approaches that worked
- Environment/configuration quirks
- Warnings or gotchas encountered

## Step 2: Find Guidance Files

```bash
find . \( -name "AGENTS.md" -o -name "CLAUDE.md" -o -name ".claude.local.md" \) 2>/dev/null | head -20
```

Decide where each addition belongs:
- `AGENTS.md` - Team-shared, harness-agnostic guidance
- `CLAUDE.md` - Team-shared Claude-specific guidance when the repo uses it
- `.claude.local.md` - Personal/local only (gitignored)

## Step 3: Draft Additions

**Keep it concise** - one line per concept. Guidance files are part of the prompt, so brevity matters.

Format: `<command or pattern>` - `<brief description>`

Avoid:
- Verbose explanations
- Obvious information
- One-off fixes unlikely to recur

## Step 4: Show Proposed Changes

For each addition:

```
### Update: ./AGENTS.md

**Why:** [one-line reason]

```diff
+ [the addition - keep it brief]
```
```

## Step 5: Apply with Approval

Ask if the user wants to apply the changes. Only edit files they approve.
