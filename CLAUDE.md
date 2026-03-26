# agent-planner-skills

## What this repo is

Full-lifecycle skill library for AgentPlanner. Covers goal definition, execution quality gates, and retrospective feedback. Built on top of [gstack](https://github.com/garrytan/gstack) by Garry Tan.

## Skill Packs Available

### Goal Setting (`/ap-clarify`, `/ap-scope`, `/ap-okr`, `/ap-anti-goal`, `/ap-assumptions`)
Run these **before** creating any AgentPlanner plan. Start every new goal with `/ap-clarify`.
Reference: `skills/goal-setting/SKILL.md`

### Execution (`/review`, `/qa`, `/ship`, `/investigate`, `/cso`, `/canary`, `/benchmark`, `/land-and-deploy`, `/document-release`)
Quality gates during build. Triggered by AP task state transitions.
Reference: `skills/execution/`

### Safety (`/careful`, `/freeze`, `/guard`, `/unfreeze`)
Guardrails for destructive operations. Use `/guard` before production work.
Reference: `skills/safety/`

### Retro (`/ap-retro`, `/ap-learn`)
Close the feedback loop after each sprint/phase.
Reference: `skills/retro/SKILL.md`

## Pipeline

```
New goal → /ap-clarify → /ap-scope → /ap-okr
         → Create AgentPlanner plan (KRs become milestones)
         → Implement tasks
         → Task in_review: /review
         → Task in_qa: /qa <staging-url>
         → Task ready_to_ship: /ship
         → Phase complete: /ap-retro
```

## AgentPlanner Integration

This repo requires the AgentPlanner MCP server. With it connected, skills automatically:
- Read task context before running (`get_task_context`)
- Post findings back as AP log entries (`add_log`)
- Update task state (`quick_status`)
- Create tasks for bugs found (`create_node`)
- Persist decisions to the knowledge graph (`add_learning`)

See `MCP.md` for the full relationship between this repo and `agent-planner-mcp`.
See `AGENTPLANNER.md` for setup instructions.

## Key Rules

- Always start a new goal with `/ap-clarify` before touching AgentPlanner
- `/ap-okr` Key Results become milestones — create them in AP immediately after
- Anti-goals from `/ap-anti-goal` go on the root node as a pinned comment
- Use `/careful` or `/guard` before any destructive operation
- After each sprint: run `/ap-retro` to feed metrics into AP knowledge layer
- Every key decision: run `/ap-learn` to persist it to the knowledge graph
