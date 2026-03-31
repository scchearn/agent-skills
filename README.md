# Agent Skills Marketplace

Private skill marketplace for Claude Code, Pi, OpenCode, and other agent workflows.

## Skills

| Skill                            | Description                                   | Status |
| -------------------------------- | --------------------------------------------- | ------ |
| [do-plan](./skills/do-plan/)     | Planning workflow for complex tasks           | Draft  |
| [do-start](./skills/do-start/)   | Task initiation and context setup             | Draft  |
| [close-off](./skills/close-off/) | Session wrap-up and Clockify timer management | Draft  |

## Usage

Skills are loaded via Pi's skill system, Claude Code's custom instructions, or OpenCode's skill registry.

## Structure

Each skill lives in `skills/<name>/` with:

- `SKILL.md` — the skill definition and prompts
- `template.json` — optional metadata/schema
