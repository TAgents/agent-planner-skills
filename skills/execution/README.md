# Execution Skills

Quality gates for the build→review→ship pipeline. Vendored from [gstack](https://github.com/garrytan/gstack) by Garry Tan (MIT). Not modified — they work exactly as documented in the gstack README.

## Skills

| Skill | Trigger in AgentPlanner | What it does |
|-------|------------------------|-------------|
| `/review` | Task → `in_review` | Pre-ship code review: SQL safety, LLM trust boundaries, auth, error handling. Auto-fixes obvious issues, flags the rest |
| `/qa <url>` | Task → `in_qa` | Real browser QA: clicks through flows, finds bugs, commits fixes, re-verifies |
| `/qa-only <url>` | Agent request type `review` | Same as /qa but report-only — no code changes |
| `/ship` | Task → `ready_to_ship` | Sync main, run tests, bump version, update CHANGELOG, push, open PR |
| `/land-and-deploy` | After PR approved | Merge PR, wait for CI + deploy, verify production health |
| `/investigate` | Task → `blocked` | Root cause debugging — traces data flow, tests hypotheses, iron law: no fixes without investigation |
| `/cso` | Phase completion | OWASP Top 10 + STRIDE threat model. 8/10+ confidence gate, independent verification |
| `/canary` | After `/land-and-deploy` | Post-deploy monitoring: console errors, performance regressions, page failures |
| `/benchmark` | Before/after PRs | Baseline page load, Core Web Vitals, resource sizes |
| `/document-release` | After `/ship` | Updates all project docs to match what was just shipped |
| `/browse` | Used by `/qa` internally | Headless Chromium browser daemon |

## How These Connect to AgentPlanner

With the AgentPlanner MCP server active in the same Claude Code session, these skills automatically integrate with your plan:

**Before running a skill** — Claude reads task context:
```
get_task_context({ node_id: "<task_id>", depth: 2 })
```

**After running a skill** — Claude updates AP state:
```
# Log what the skill found
add_log({ plan_id, node_id, content: "review: fixed 2 issues, flagged 1 race condition", log_type: "progress" })

# Update task status
quick_status({ node_id, status: "in_review" })  // or completed, blocked, etc.

# Bug found by /qa? Create a child task
create_node({ parent_id: phase_id, title: "Fix: [bug description]", node_type: "task" })

# Key decision during /investigate? Persist to knowledge graph
add_learning({ content: "Root cause was X because Y", plan_id, node_id })
```

The agent handles this wiring naturally — no custom configuration needed.

## Keeping Skills Up to Date

```bash
# Add gstack as upstream remote
git remote add gstack https://github.com/garrytan/gstack.git
git fetch gstack

# Update a specific skill
cp -r /path/to/gstack/review/* skills/execution/review/
git add skills/execution/review && git commit -m "chore: sync /review from gstack upstream"
```

## Credits

All skills in this directory are from [gstack](https://github.com/garrytan/gstack) by [Garry Tan](https://x.com/garrytan). MIT licensed. Original source: https://github.com/garrytan/gstack
