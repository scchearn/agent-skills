# Agent Planning Skill Framework

Reusable workflow skills for agent coding harnesses compatible with Anthropic's skill layout.

These skills help an agent research a problem, save the findings, turn them into a concrete plan, execute that plan, and update that plan when the work changes.

## Quick Start

If your harness supports Anthropic-style `SKILL.md` skills, load this repo's `skills/` directory using that harness's normal skill setup.

Once the skills are available, a typical flow looks like this:

```text
/do-research how this app authenticates users
/do-plan add audit logging for failed sign-ins
/do-start plans/audit-logging-failed-sign-ins.md
/do-amend plans/audit-logging-failed-sign-ins.md
```

`/do-research` writes a reusable research note to `plans/research/`. Later, `/do-plan` looks for relevant research notes there before it creates a plan.

Many harnesses expose skills as slash commands. If yours uses a different invocation style, use the same skill names and arguments in the format your harness expects.

## Which Skill Should I Use?

| Skill | Use it when | What you get |
| --- | --- | --- |
| [do-research](./skills/do-research/) | You need evidence before deciding what to build or change | A research note in `plans/research/` with findings, constraints, and the next recommended step |
| [do-plan](./skills/do-plan/) | You have a feature, bug fix, or task that should be broken into ordered steps | A plan file in `plans/` with concrete tasks, verification steps, and links to any research it used |
| [do-start](./skills/do-start/) | You want the agent to execute the next unblocked task from a plan | Autonomous task execution with verification and plan updates |
| [do-amend](./skills/do-amend/) | A plan already exists, but requirements or scope changed | An impact analysis, then a safe update to the plan after approval |

## Recommended Workflow

1. Start with `/do-research <topic>` when the workspace or external behavior is unclear.
   Example: `/do-research how billing webhooks are validated today`
2. Run `/do-plan <task description>` to create a plan. It will reuse relevant notes from `plans/research/` when they exist.
3. Run `/do-start plans/<slug>.md` to execute the plan in order.
4. Run `/do-amend plans/<slug>.md` if new requirements appear or completed work needs to be revisited.

You do not need to use all four skills every time. If the work is already well understood, it is fine to start with `/do-plan`.

## Common Examples

```text
/do-research how billing webhooks are validated today
/do-plan add CSV export to the admin orders page
/do-start plans/admin-orders-csv-export.md
/do-start plans/admin-orders-csv-export.md T3
/do-start plans/admin-orders-csv-export.md T3,T5
/do-start plans/admin-orders-csv-export.md T3-T5
```

For `/do-amend`, describe the change first, then invoke the skill against the existing plan:

```text
We now need the CSV export to include refunded orders too.
/do-amend plans/admin-orders-csv-export.md
```

## What These Skills Create

- `plans/research/<slug>.md` for reusable research findings
- `plans/<slug>.md` for the working plan
- `plans/INDEX.md` for a simple overview of every plan, so you can quickly see what is pending, in progress, or done

This keeps research, planning, and execution state in the repository instead of burying it in chat history. Research notes live alongside plans, while `plans/INDEX.md` gives you one place to check overall plan progress.

## Typical Structure

After you start using the framework, you will typically have:

```text
.
├── README.md
├── plans/
│   ├── INDEX.md
│   └── research/
└── skills/
    ├── do-amend/
    │   └── SKILL.md
    ├── do-plan/
    │   └── SKILL.md
    ├── do-research/
    │   └── SKILL.md
    └── do-start/
        └── SKILL.md
```

## Naming Collisions

Different agent coding harnesses handle duplicate skill names differently. Some prefer higher-precedence locations. Others warn and use the last-loaded definition.

The safe rule is simple: keep skill names unique across your global, shared, and project-level skill sets. Do not rely on collision behavior as an override mechanism.
