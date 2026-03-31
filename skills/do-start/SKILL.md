# do-start Skill

## Purpose

Initialize a work session with proper context and tool setup.

## Trigger

When the user says something like:
- "Let's start on..."
- "I want to work on..."
- "Begin task..."

## Workflow

1. **Identify** — determine the task/project
2. **Gather** — pull relevant context (TW tasks, calendar, notes)
3. **Setup** — ensure tools ready (Clockify timer, MCPs, etc.)
4. **Confirm** — summarize context before proceeding

## Context Checklist

- [ ] TaskWarrior tasks for this project
- [ ] Calendar events (today/next)
- [ ] Relevant notes from `notes/` or `plans/`
- [ ] Clockify timer started (if used)
