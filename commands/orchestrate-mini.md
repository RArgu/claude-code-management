# /orchestrate-mini — Lightweight Plan-Execute-Review

Lightweight orchestration for a single-task change — no agents, no plan file,
no new test suite. For multi-module builds, use `/orchestrate`.

## When to use
Bug fixes, config changes, data corrections, small features, script additions.
Not for multi-module features or new subsystems.

## Steps

### 1. What
State the change in 1-2 sentences. What exactly will change?

### 2. How
Which files, which functions, what the diff looks like — specific enough that
the user can approve or redirect.

### 3. Critique
Challenge your own plan:
- What could go wrong? What edge cases exist?
- Does this conflict with `DECISIONS.md`?
- Is there a simpler approach?
- What assumptions are you making that you haven't verified?

### 4. Reevaluate
Revise the plan based on the critique. If it found real issues, state what
changed. If not, confirm the plan stands.

### 5. Execute
Make the change. Run the tests.

### 6. Review
Verify it worked: tests pass, output matches expectations, and the affected
code path actually runs clean.

Report: what was done, what the result was, any remaining issues.
