# Retro Skills for AgentPlanner

These skills close the feedback loop — feeding execution results back into the AgentPlanner knowledge graph so future planning improves.

---

## /ap-retro

**Purpose:** Run a structured sprint retrospective and feed metrics into the AgentPlanner knowledge layer.

**When to use:** After each sprint or phase completion.

**Process:**
1. Gather metrics from the sprint:
   - Tasks completed vs planned
   - Any blockers encountered + how resolved
   - Velocity (if measurable)
   - Test coverage delta (if applicable)
   - Key decisions made

2. If gstack's `/retro` was run during the sprint, include those metrics (LOC, commits, test health).

3. Score progress against the plan's OKRs (Key Results):
   - For each KR: current value vs target
   - Overall OKR health: on track / at risk / off track

4. Post a retrospective episode to the AP knowledge graph:
   ```
   POST /knowledge/episodes
   {
     "content": "[Sprint N Retro]\n\nCompleted: X/Y tasks\nBlockers: ...\nOKR progress: KR1 at X%, KR2 at Y%\nKey decisions: ...\nNext sprint focus: ...",
     "name": "Sprint [N] Retrospective",
     "plan_id": "[current plan]",
     "metadata": {"type": "retro", "tags": ["sprint", "retro"]}
   }
   ```

5. Post an OKR evaluation to the plan's goal:
   ```
   POST /goals/v2/{goal_id}/evaluations
   {
     "score": [0.0-1.0],
     "notes": "OKR progress: KR1 X%, KR2 Y%, KR3 Z%. Overall on track / at risk."
   }
   ```

6. Share a brief retro summary with the team (3 lines max):
   - What went well
   - What to improve
   - Focus for next sprint

**Output:** Knowledge episode + goal evaluation in AP

---

## /ap-learn

**Purpose:** Distill key learnings from completed work into AP's knowledge graph for future agent sessions.

**When to use:** After completing a significant task, making a key decision, or discovering something unexpected.

**Process:**
1. Identify what's worth preserving:
   - Decisions made and why (the reasoning, not just the outcome)
   - Approaches that didn't work (saves future agents from repeating)
   - Discoveries about the codebase, users, or problem
   - Dependencies or constraints uncovered

2. For each learning, create a knowledge episode:
   ```
   POST /knowledge/episodes
   {
     "content": "[Clear statement of the learning]\n\nContext: [why this came up]\nImplication: [what this means for future work]",
     "name": "[Short descriptive title]",
     "plan_id": "[current plan]",
     "node_id": "[relevant task, if applicable]",
     "metadata": {"type": "learning", "tags": ["relevant", "tags"]}
   }
   ```

3. For significant decisions, use type "decision":
   ```
   metadata: {"type": "decision", "tags": ["architecture", "..."]
   ```

4. Verify it's searchable:
   ```
   POST /knowledge/graph-search
   {"query": "[key term from the learning]"}
   ```
   Confirm it appears in results.

**Output:** Knowledge episodes stored in AP — available to any future agent via get_task_context or graph-search
