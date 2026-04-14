# Agent Markdown Management Plugin

Tools to maintain and improve agent guidance markdown files such as `AGENTS.md` and `CLAUDE.md` by auditing quality, capturing session learnings, and keeping project memory current.

## What It Does

Two complementary skills for different purposes:

| | agent-md-improver (skill) | revise-agent-md (skill) |
|---|---|---|
| **Purpose** | Keep agent guidance files aligned with codebase | Capture session learnings |
| **Triggered by** | Codebase changes | Session discoveries |
| **Use when** | Periodic maintenance | Session revealed missing context |

## Usage

### Skill: agent-md-improver

Audits guidance files against current codebase state:

```
"audit my agent guidance files"
"check if my AGENTS.md is up to date"
"check if my CLAUDE.md is up to date"
```

### Skill: revise-agent-md

Captures learnings from the current session:

```
"use the revise-agent-md skill"
"capture guidance updates from this session"
```

## Author

Isabella He (isabella@anthropic.com)
