# agent-planner-skills + agent-planner-mcp: How They Relate

Two repos, one system. Here's exactly how they fit together.

## The Relationship

```
agent-planner-skills          agent-planner-mcp
──────────────────            ─────────────────
WHAT to do                    HOW to talk to AgentPlanner
(workflows, process)          (MCP tools, API interface)

/ap-clarify  ──────────────→  create_node (milestones from OKRs)
/ap-okr      ──────────────→  create_node + add_learning
/review      ──────────────→  quick_status + add_log
/qa          ──────────────→  create_node (bug tasks) + add_log
/ship        ──────────────→  quick_status (completed) + add_log
/ap-retro    ──────────────→  add_learning + evaluate_goal
```

**agent-planner-mcp** is the language — it defines the MCP tools (`get_task_context`, `quick_status`, `add_learning`, etc.) that agents use to read/write AgentPlanner state.

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
│  (MCP tools: get_task_context,  │    Tools that Claude calls
│   quick_status, add_learning…)  │
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
| `/ap-okr` | `create_node` (creates milestones), `add_learning` (saves OKRs) |
| `/ap-anti-goal` | `add_log` (pinned comment on root node) |
| `/ap-assumptions` | `create_node` (creates risk tasks for each unvalidated assumption) |

### Execution Skills (gstack)

| Skill | MCP tools called |
|-------|----------------|
| `/review` | `get_task_context`, `quick_status` (→ in_review), `add_log` (findings) |
| `/qa` | `get_task_context`, `create_node` (bug tasks), `add_log` |
| `/ship` | `quick_status` (→ completed), `add_log` (PR link) |
| `/investigate` | `get_task_context`, `quick_status` (→ blocked/in_progress), `add_learning` (root cause) |
| `/cso` | `create_node` (security findings as tasks), `add_learning` |

### Retro Skills

| Skill | MCP tools called |
|-------|----------------|
| `/ap-retro` | `add_learning` (sprint episode), `evaluate_goal` (OKR score) |
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

MCP server connected. Use get_task_context, quick_status, add_learning, suggest_next_tasks
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
