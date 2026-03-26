# agent-planner-skills

A full-lifecycle skill library for [AgentPlanner](https://agentplanner.io) — covering goal definition, execution quality gates, and retrospective feedback.

Built on top of [gstack](https://github.com/garrytan/gstack) by Garry Tan. We vendor gstack's execution skills unchanged and add AgentPlanner-native goal-setting and retro skills on top.

## Philosophy

**gstack** gives you opinionated execution: review → QA → ship with quality gates at every step.  
**AgentPlanner** gives you coordination: plans, phases, tasks, milestones, and a knowledge graph.  
**agent-planner-skills** connects them into a full pipeline — from fuzzy goal to shipped feature, with every step tracked.

```
Goal → [ap-clarify] → [ap-scope] → [ap-okr]
     → AgentPlanner plan (phases + tasks + milestones)
     → Claude Code + gstack: [/review] → [/qa] → [/ship]
     → [ap-retro] → AP knowledge layer
```

## Skill Packs

| Pack | Location | What it does |
|------|----------|-------------|
| **Goal Setting** | `skills/goal-setting/` | Define goals before planning: clarify, scope, OKR generation, anti-goals, assumption audit |
| **Execution** | `skills/execution/` | Quality gates during build: review, QA, ship, security (vendored from gstack) |
| **Retro** | `skills/retro/` | Close the loop: retro metrics → AP knowledge, findings → AP tasks |

## Setup

### Requirements
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
- [AgentPlanner](https://agentplanner.io) account + API token
- [gstack](https://github.com/garrytan/gstack) installed

### Install

```bash
# Clone into your Claude skills directory
git clone https://github.com/TAgents/agent-planner-skills.git ~/.claude/skills/agent-planner-skills

# Or add to your project
git clone https://github.com/TAgents/agent-planner-skills.git .claude/skills/agent-planner-skills
```

### Connect AgentPlanner MCP

Add to your `claude_desktop_config.json` or `.mcp.json`:

```json
{
  "mcpServers": {
    "agent-planner": {
      "command": "npx",
      "args": ["-y", "@tagents/agent-planner-mcp"],
      "env": {
        "AGENTPLANNER_TOKEN": "your-token-here"
      }
    }
  }
}
```

That's the bridge. With both gstack and the AP MCP server active in the same Claude Code session, the agent can:
- Read task context from AP (`get_task_context`)
- Run gstack quality gates (`/review`, `/qa`, `/ship`)
- Update AP task state and log progress

No custom code needed — the agent handles the wiring naturally.

### Add to your project's CLAUDE.md

```markdown
## agent-planner-skills

Goal-setting skills: /ap-clarify, /ap-scope, /ap-okr, /ap-anti-goal, /ap-assumptions
Execution skills (gstack): /review, /qa, /ship, /cso, /retro
Retro skills: /ap-retro, /ap-learn

Always start a new goal with /ap-clarify before creating an AgentPlanner plan.
When a task moves to in_review, run /review. When in_qa, run /qa. When ready_to_ship, run /ship.
After a sprint, run /ap-retro to feed metrics back into the AP knowledge layer.
```

## How It Fits Together

See [AGENTPLANNER.md](./AGENTPLANNER.md) for the full integration guide — lifecycle hooks, MCP config, and the step-by-step walkthrough.

## Credits

Execution skills vendored from [gstack](https://github.com/garrytan/gstack) by [Garry Tan](https://x.com/garrytan) — MIT licensed.

## License

MIT
