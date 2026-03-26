# Safety Skills

Guardrails for destructive operations and scoped editing. Vendored from [gstack](https://github.com/garrytan/gstack) by Garry Tan (MIT). Especially useful during `/investigate` and production work.

| Skill | What it does |
|-------|-------------|
| `/careful` | Warns before `rm -rf`, `DROP TABLE`, force-push, etc. Say "be careful" to activate |
| `/freeze` | Restrict file edits to one directory — prevents accidental changes during debugging |
| `/guard` | `/careful` + `/freeze` combined — maximum safety for production work |
| `/unfreeze` | Remove the `/freeze` boundary |

## When to use in AP context

- Before `/investigate` on a production issue: `/guard`
- When fixing a bug in a single service: `/freeze services/auth`
- Any time a task is marked `blocked` in production: `/careful` minimum

Credits: [gstack](https://github.com/garrytan/gstack) by Garry Tan. MIT.
