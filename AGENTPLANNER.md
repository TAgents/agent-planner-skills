# AgentPlanner Integration Guide

This guide walks you through connecting agent-planner-skills to AgentPlanner so every skill is aware of your plan context and can update task state automatically.

## Prerequisites

- AgentPlanner account → get your API token at [agentplanner.io/app/settings](https://agentplanner.io/app/settings)
- Claude Code installed
- gstack installed (`~/.claude/skills/gstack`)
- This repo cloned to `~/.claude/skills/agent-planner-skills`

---

## Step 1: Connect the AgentPlanner MCP Server

The MCP server is the bridge between Claude Code and AgentPlanner. With it active, Claude can read your plan context and update task state during skill execution.

**Option A: Claude Desktop (`~/Library/Application Support/Claude/claude_desktop_config.json`)**

```json
{
  "mcpServers": {
    "agent-planner": {
      "command": "npx",
      "args": ["-y", "@tagents/agent-planner-mcp"],
      "env": {
        "AGENTPLANNER_TOKEN": "your-token-here",
        "AGENTPLANNER_API_URL": "https://agentplanner.io/api"
      }
    }
  }
}
```

**Option B: Project-level `.mcp.json`**

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

Restart Claude Code after adding the config.

---

## Step 2: Add to your project's CLAUDE.md

```markdown
## agent-planner-skills

Use these skills for all planning and execution work:

### Goal Setting (run before creating any AgentPlanner plan)
- /ap-clarify — Clarify the goal with 5 forcing questions
- /ap-scope — Define explicit time/size scope  
- /ap-okr — Generate Objective + Key Results → become AP milestones
- /ap-anti-goal — Define what we are NOT doing
- /ap-assumptions — Surface hidden assumptions as risk tasks

### Execution (triggered by AP task state transitions)
- /review — Run when task moves to in_review
- /qa <staging-url> — Run when task moves to in_qa
- /ship — Run when task is ready_to_ship
- /cso — Run security audit at end of each implementation phase

### Retro (run after each sprint/phase)
- /ap-retro — Feed sprint metrics into AP knowledge layer
- /ap-learn — Distill findings into AP knowledge episodes
```

---

## Step 3: The Full Pipeline

Here's how a goal moves from idea to shipped feature:

### 1. Goal Definition

```
You: I want to build X

Run /ap-clarify
→ Claude asks 5 questions: who is it for, what pain, success in 30 days, out of scope, riskiest assumption
→ Outputs a goal brief

Run /ap-scope  
→ Is this 1 week, 1 month, or 1 quarter?
→ Sets expectations for plan size

Run /ap-okr
→ Generates: 1 Objective + 3-5 Key Results
→ Key Results become milestones in your AP plan
```

### 2. Plan Creation

Take the goal brief from step 1 and create your AgentPlanner plan. The KRs from `/ap-okr` become your milestones. The anti-goals from `/ap-anti-goal` go in the plan description as explicit constraints.

### 3. Execution (gstack quality gates)

As you work through tasks, gstack skills gate each stage:

```
Task → in_progress: implement the feature
Task → in_review: run /review
  → Claude finds bugs, auto-fixes obvious ones, flags others
  → Results logged to AP task

Task → in_qa: run /qa https://staging.yourapp.com
  → Real browser, real clicks, finds bugs
  → Each bug → new AP child task created automatically

Task → ready_to_ship: run /ship
  → Syncs main, runs tests, opens PR
  → PR link stored as AP task artifact
  → Task → completed
```

### 4. Retrospective

After each sprint or phase:

```
Run /ap-retro
→ Metrics (LOC, commits, test coverage) posted to AP knowledge graph
→ Per-task breakdown logged

Run /ap-learn
→ Key decisions and lessons from this sprint → AP knowledge episodes
→ Available to future agents via get_task_context
```

---

## Lifecycle Hook Reference

| AP Task State | Skill to Run | What Happens |
|--------------|-------------|-------------|
| `not_started` | `/ap-clarify` (on goal) | Goal brief created |
| `not_started` | `/ap-okr` (on goal) | Milestones generated |
| `in_progress` | `/cso` (on phase end) | Security audit runs |
| `in_review` | `/review` | Bug review + auto-fix |
| `in_qa` | `/qa <url>` | Browser QA + bug tasks |
| `ready_to_ship` | `/ship` | PR opened + task completed |
| Phase complete | `/ap-retro` | Metrics → AP knowledge |

---

## Vendored gstack Skills

The `skills/execution/` directory contains skills vendored from [gstack](https://github.com/garrytan/gstack) by Garry Tan (MIT). These are not modified — they work exactly as documented in the gstack README.

To pull upstream gstack updates:

```bash
cd ~/.claude/skills/agent-planner-skills
git remote add gstack https://github.com/garrytan/gstack.git
git fetch gstack
# Cherry-pick or copy specific skill updates as needed
```

---

## Troubleshooting

**Skills not showing in Claude Code?**  
Make sure the repo is in `~/.claude/skills/` and your CLAUDE.md references them.

**MCP connection not working?**  
Check `AGENTPLANNER_TOKEN` is set. Test: `curl -H "Authorization: Bearer $AGENTPLANNER_TOKEN" https://agentplanner.io/api/dashboard/summary`

**gstack skills missing?**  
Run `cd ~/.claude/skills/gstack && ./setup`
