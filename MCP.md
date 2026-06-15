# agent-planner-skills + agent-planner-mcp: How They Relate

Two repos, one system. Here's exactly how they fit together.

## The Relationship

```
agent-planner-skills          agent-planner-mcp
──────────────────            ─────────────────
WHAT to do                    HOW to talk to AgentPlanner
(workflows, process)          (MCP tools, API interface)

/ap-clarify  ──────────────→  extend_intention (milestones from OKRs)
/ap-okr      ──────────────→  extend_intention + add_learning
/review      ──────────────→  update_task (status + log_message)
/qa          ──────────────→  extend_intention (bug tasks) + update_task
/ship        ──────────────→  update_task (status=completed + log_message)
/ap-retro    ──────────────→  add_learning + goal_state
```

**agent-planner-mcp** is the language — it defines the MCP tools (`task_context`, `update_task`, `add_learning`, etc.) that agents use to read/write AgentPlanner state.

**agent-planner-skills** is the process — it defines *when* to run which skill, *what* the skill should do, and *how* to feed results back into AgentPlanner using those MCP tools.

You need both. The skills describe the workflow; the MCP server executes the AP state changes.

## The Three-Layer Stack

```
┌─────────────────────────────────┐
│       agent-planner-skills      │  ← You are here
│  (goal-setting, execution,      │    Process + workflows
│   retro workflows)              │
├─────────────────────────────────┤
│       agent-planner-mcp         │  ← The bridge
│  (MCP tools: task_context,      │    Tools that Claude calls
│   update_task, add_learning…)   │
├─────────────────────────────────┤
│       AgentPlanner REST API     │  ← The backend
│  (plans, nodes, knowledge       │    Source of truth
│   graph, goals…)                │
└─────────────────────────────────┘
```

## Which MCP Tools Each Skill Uses

### Goal Setting Skills

| Skill | MCP tools called |
|-------|----------------|
| `/ap-clarify` | `add_learning` (saves goal brief to knowledge graph) |
| `/ap-scope` | `add_learning` (saves scope decision) |
| `/ap-okr` | `extend_intention` (creates milestones), `add_learning` (saves OKRs) |
| `/ap-anti-goal` | `update_task` (pinned comment on root node via `log_message`) |
| `/ap-assumptions` | `extend_intention` (creates risk tasks for each unvalidated assumption) |

### Execution Skills (gstack)

| Skill | MCP tools called |
|-------|----------------|
| `/review` | `task_context`, `update_task` (→ in_review status + findings log_message) |
| `/qa` | `task_context`, `extend_intention` (bug tasks), `update_task` (log) |
| `/ship` | `update_task` (→ completed status + PR link log_message) |
| `/investigate` | `task_context`, `update_task` (→ blocked/in_progress), `add_learning` (root cause) |
| `/cso` | `extend_intention` (security findings as tasks), `add_learning` |

### Retro Skills

| Skill | MCP tools called |
|-------|----------------|
| `/ap-retro` | `add_learning` (sprint episode), `goal_state` (read OKR progress), `update_goal` (status if achieved) |
| `/ap-learn` | `add_learning` (knowledge episodes) |

## Setup: Getting Both Working Together

### 1. Install agent-planner-mcp (the MCP server)

```bash
# Add to your Claude Code MCP config (.mcp.json or claude_desktop_config.json)
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

The MCP server's `SKILL.md` (at `node_modules/@tagents/agent-planner-mcp/SKILL.md`) loads automatically in Claude Code, giving the agent its tool reference.

### 2. Install agent-planner-skills (this repo)

```bash
git clone https://github.com/TAgents/agent-planner-skills.git ~/.claude/skills/agent-planner-skills
```

### 3. Add both to your CLAUDE.md

```markdown
## AgentPlanner

MCP server connected. Use task_context, update_task, add_learning, claim_next_task
for all AP state reads/writes. See agent-planner-mcp SKILL.md for full tool reference.

## agent-planner-skills

Goal setting: /ap-clarify, /ap-scope, /ap-okr, /ap-anti-goal, /ap-assumptions
Execution: /review, /qa, /qa-only, /ship, /land-and-deploy, /investigate, /cso
Safety: /careful, /freeze, /guard, /unfreeze
Retro: /ap-retro, /ap-learn

Pipeline: always start with /ap-clarify → run skills match AP task state → close with /ap-retro
```

## Why Two Repos?

- **agent-planner-mcp** is infrastructure — it stays stable, versioned, published to npm. Any MCP client (Claude Desktop, Cursor, etc.) can use it.
- **agent-planner-skills** is process — it evolves as best practices develop. It's opinionated about *how* to work, not just *how to call the API*.

Keeping them separate means you can update the MCP server (new API endpoints, bug fixes) without touching the skill workflows, and vice versa.

## Links

- agent-planner-mcp: https://github.com/TAgents/agent-planner-mcp
- agent-planner-skills: https://github.com/TAgents/agent-planner-skills
- AgentPlanner: https://agentplanner.io
