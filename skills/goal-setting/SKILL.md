# Goal Setting Skills for AgentPlanner

These skills run **before** creating an AgentPlanner plan. They transform a vague goal into a well-defined, structured brief that becomes the foundation of your plan.

Run them in order: clarify → scope → okr → anti-goal → assumptions

---

## /ap-clarify

**Purpose:** Force 5 clarifying questions before any planning begins. Surfaces the real goal, not the stated one.

**When to use:** Whenever someone says "I want to build X" or "we should do Y."

**Process:**
1. Ask these 5 questions (one at a time, wait for answers):
   - **Who** is this specifically for? (not "developers" — which developers, at what stage, with what problem)
   - **What specific pain** does it solve? Give me an example from the last 7 days where you felt this pain.
   - **What does success look like in 30 days?** Be concrete — what will you see/measure?
   - **What is explicitly out of scope?** What are you tempted to add but shouldn't?
   - **What's the riskiest assumption** you're making? What if it's wrong?

2. After all 5 answers, synthesize a Goal Brief:
   ```
   GOAL BRIEF
   ----------
   Core problem: [1 sentence]
   Target user: [specific]
   Success in 30 days: [measurable]
   Out of scope: [explicit list]
   Riskiest assumption: [+ mitigation]
   Recommended next step: [/ap-scope or directly to /ap-okr]
   ```

3. Ask: "Does this capture what you're trying to do? Anything to adjust?"

**Output:** Goal Brief (use this as the AgentPlanner plan description)

---

## /ap-scope

**Purpose:** Force an explicit scope decision. Prevents plans that are secretly 3 goals dressed as one.

**When to use:** After /ap-clarify, before /ap-okr.

**Process:**
1. Present the 3 options clearly:
   - **1 week:** What's the smallest useful thing that proves the concept?
   - **1 month:** What's the meaningful version that delivers real value?
   - **1 quarter:** What's the full vision?

2. For each option, sketch:
   - What's included
   - What's cut
   - What the deliverable looks like

3. Make a recommendation based on the Goal Brief. Explain why.

4. Get explicit confirmation: "We're building the [X-week] version. Everything else goes on the backlog."

**Output:** Scope decision + what's explicitly deferred → add to plan description as constraints

---

## /ap-okr

**Purpose:** Transform a scoped goal into a measurable Objective + Key Results. KRs become milestones in AgentPlanner.

**When to use:** After /ap-scope.

**Process:**
1. Write 1 Objective: inspiring, qualitative, time-bound. "By [date], we will [outcome]."

2. Generate 3-5 Key Results:
   - Each must be measurable (number, percentage, binary yes/no)
   - Each must be achievable within the scope decision
   - Each should be independently verifiable
   - Format: "KR1: [metric] goes from [baseline] to [target] by [date]"

3. Sanity check: "If we hit all 5 KRs, would you call this a success? Is any KR achievable without the others?"

4. Map KRs to AgentPlanner milestones:
   ```
   Create milestone for each KR in the AP plan.
   Set milestone title = KR description.
   Set milestone description = how to verify this KR is met.
   ```

**Output:** 1 Objective + 3-5 Key Results → create as AP milestones immediately

---

## /ap-anti-goal

**Purpose:** Define what you are explicitly NOT building. Prevents scope creep during execution.

**When to use:** After /ap-okr, before planning phases/tasks.

**Process:**
1. Ask: "What are you tempted to add but we agreed is out of scope?"
2. Ask: "What will stakeholders ask for that we'll need to say no to?"
3. Ask: "What would make this twice as long to build?"

4. Compile an Anti-Goal List:
   ```
   ANTI-GOALS (explicitly out of scope)
   ✗ [Feature/capability] — deferred to [v2/next quarter/separate plan]
   ✗ [Feature/capability] — not solving this problem
   ✗ [Feature/capability] — too complex for current scope
   ```

5. Add this list as a pinned comment on the AP plan root node.

**Output:** Anti-Goal List pinned to AP plan — referenced whenever scope creep appears

---

## /ap-assumptions

**Purpose:** Surface hidden assumptions in the plan. Each becomes either a validated fact or a risk task.

**When to use:** After anti-goals are defined, before breaking down phases/tasks.

**Process:**
1. List every assumption embedded in the goal brief and OKRs:
   - Technical assumptions ("this API exists / works as documented")
   - User assumptions ("users will actually do X")
   - Resource assumptions ("we have time / people / budget for this")
   - Dependency assumptions ("Y will be ready when we need it")

2. For each assumption, classify:
   - **Validated:** We know this is true (cite evidence)
   - **Testable:** We can verify this quickly (define the test)
   - **Risk:** We can't validate this fast enough (create a risk task)

3. For each Risk assumption, create an AP task:
   ```
   Title: "Validate assumption: [assumption]"
   Description: How to test this, what we'll do if it's wrong, deadline for deciding
   Status: not_started
   Priority: high (do these first)
   ```

**Output:** Assumption audit + risk tasks created in AP plan
