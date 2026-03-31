# do-plan Skill

## Purpose

Structured planning workflow for complex, multi-step tasks.

## Trigger

When the user says something like:
- "Let's plan this"
- "I need a plan for..."
- "Help me break this down"

## Workflow

1. **Clarify** — ask questions until the goal is clear
2. **Decompose** — break into discrete steps
3. **Sequence** — order steps by dependency
4. **Assign** — suggest owners if team context known
5. **Output** — deliver as:
   - TaskWarrior tasks (if project context)
   - Markdown plan (if exploratory)
   - Calendar blocks (if time-bound)

## Template

```markdown
# Plan: {TITLE}

## Goal
{one sentence}

## Steps
- [ ] {step 1}
- [ ] {step 2}
...

## Success Criteria
- {measurable outcome}
```
