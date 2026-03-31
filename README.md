# Agent Skills Marketplace

Private skill marketplace for Claude Code, Pi, OpenCode, and other agent workflows.

## Skills

| Skill                                  | Description                                                      | Status |
| -------------------------------------- | ---------------------------------------------------------------- | ------ |
| [do-plan](./skills/do-plan/)           | Planning workflow for complex tasks                              | Draft  |
| [do-start](./skills/do-start/)         | Execute the next unblocked task from a plan                      | Draft  |
| [do-amend](./skills/do-amend/)         | Amend a plan after requirements or scope change                  | Draft  |
| [do-research](./skills/do-research/)   | Research workspace context, APIs, and external evidence before action | Draft  |
| [close-off](./skills/close-off/)       | Session wrap-up and Clockify timer management                    | Draft  |

## Usage

Skills are loaded via Pi's skill system, Claude Code's custom instructions, or OpenCode's skill registry.

In Claude Code plugin structure, slash commands and skills are distinct: commands live under `commands/`, while reusable skills live under `skills/<name>/`. This marketplace keeps these workflows as skills, and host environments may choose to expose them through command-like entrypoints such as `/do-plan`.

## Structure

Each skill lives in `skills/<name>/` with:

- `SKILL.md` — the skill definition and prompts
- optional bundled resources in subdirectories such as `references/`, `examples/`, `scripts/`, or `assets/`

Example:

```text
skills/
  do-plan/
    SKILL.md
    references/
      template.md
```
