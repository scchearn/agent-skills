# Agent Skills Marketplace

Private skill marketplace for Claude Code, Pi, OpenCode, and other agent workflows.

## Skills

| Skill                                | Description                                                           | Status |
| ------------------------------------ | --------------------------------------------------------------------- | ------ |
| [do-plan](./skills/do-plan/)         | Planning workflow for complex tasks                                   | Draft  |
| [do-start](./skills/do-start/)       | Execute the next unblocked task from a plan                           | Draft  |
| [do-amend](./skills/do-amend/)       | Amend a plan after requirements or scope change                       | Draft  |
| [do-research](./skills/do-research/) | Research workspace context, APIs, and external evidence before action | Draft  |
| [close-off](./skills/close-off/)     | Session wrap-up and Clockify timer management                         | Draft  |

## Usage

Skills are loaded via Pi's skill system, Claude Code's custom instructions, or OpenCode's skill registry.

In Claude Code plugin structure, slash commands and skills are distinct: commands live under `commands/`, while reusable skills live under `skills/<name>/`. This marketplace keeps these workflows as skills, and host environments may choose to expose them through command-like entrypoints such as `/do-plan`.

## Name Collisions

If the same skill name exists in multiple locations, precedence depends on the host:

- **Claude Code:** documented precedence is `enterprise > personal/global > project`. Plugin skills are namespaced as `plugin-name:skill-name`, so they do not collide with non-plugin skills.
- **OpenCode:** duplicate names are not a formal override mechanism. OpenCode warns on duplicates and the last-loaded definition wins, so behavior depends on discovery order.

Recommendation: keep skill names unique across global and project locations rather than relying on override behavior.

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
