# Execution Skills

These skills are vendored from [gstack](https://github.com/garrytan/gstack) by Garry Tan (MIT license). They are not modified.

Install gstack to use them:

```bash
git clone https://github.com/garrytan/gstack.git ~/.claude/skills/gstack
cd ~/.claude/skills/gstack && ./setup
```

## Skills in this pack (via gstack)

| Skill | When to run in AgentPlanner |
|-------|---------------------------|
| `/review` | Task moves to `in_review` |
| `/qa <url>` | Task moves to `in_qa` |
| `/ship` | Task is `ready_to_ship` |
| `/cso` | End of each implementation phase |
| `/investigate` | Task is `blocked` — root cause debugging |
| `/retro` | End of sprint (combine output with `/ap-retro`) |

## How gstack + AgentPlanner connect

No custom code needed. With both gstack and the AgentPlanner MCP server active in the same Claude Code session:

1. Claude reads task context from AP (`get_task_context`)
2. Runs the appropriate gstack skill
3. Posts results back to AP (task log, status update, new tasks for bugs found)

The agent handles this naturally — just make sure both are configured in your `CLAUDE.md`.

## Pulling upstream gstack updates

```bash
cd ~/.claude/skills/agent-planner-skills
git remote add gstack https://github.com/garrytan/gstack.git
git fetch gstack
# Selectively apply updates to skills/execution/ as needed
```

## Credits

gstack by [Garry Tan](https://x.com/garrytan) — [github.com/garrytan/gstack](https://github.com/garrytan/gstack). MIT licensed.
