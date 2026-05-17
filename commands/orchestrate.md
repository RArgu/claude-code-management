# /orchestrate — Plan, Build, Test, Review

Multi-agent workflow for building a non-trivial feature that spans multiple
modules. For a single-task change, use `/orchestrate-mini` instead.

## Phase 1: Plan

Before spawning any agents, complete this with the user:

### 1.1 Scope
- Read `DECISIONS.md` — confirm no conflict with the planned work.
- Interview the user: what are we building, what is explicitly out of scope?
- Separate **leaf modules** (self-contained units) from **integration modules**
  (code that wires leaf modules together).

### 1.2 Interface contracts
Before any agent starts, define for every module:
- **Signatures** — exact names, parameters with types, return types.
- **Data structures** — every field name, type, and default for anything that
  crosses a module boundary.
- **Data flow** — which module produces what, which consumes it, in what format.
- **Imports** — which modules this code may import (enforce layer rules).

### 1.3 Success criteria
For each task: what does "done" look like, and what must NOT break?

Write the plan to a file (e.g. `docs/plan_<feature>.md`) so it survives context
compression.

## Phase 2: Build–Test–Review

For each task, run three agents sequentially on `main` (no worktree isolation —
worktrees leak commits):

### Build agent
Receives the plan, interface contracts, exact signatures, and code standards.
Produces committed code on `main` and reports the commit hash. Commits before
finishing.

### Test agent
Receives the build commit, the contracts, and the adjacent modules. Writes a
persistent, integration-focused test file — test the module *with* its
neighbours, not in isolation — runs it, reports pass/fail, and commits.

### Review agent
Receives both commit hashes, the contracts, and the success criteria. Checks
the code against the contract, looks for integration mismatches, P0 compliance,
and size/import rules. Produces a PASS / FAIL / WARN report. Does not fix —
reports only.

### Fix
- **Leaf issue** (self-contained within one file): resume the build agent with
  the review feedback.
- **Boundary issue** (cross-module): the orchestrator fixes it directly.

Re-run the test agent after fixes.

## Phase 3: Integration validation

After all tasks complete: run the full test suite and the entry-point smoke
checks; confirm no regressions in existing functionality.

## Principles

- **Agents build leaf modules; the orchestrator writes integration code.**
  Agents are reliable within a single file and unreliable at module
  boundaries — across projects using this workflow, roughly two-thirds of
  multi-module agent tasks introduced a boundary bug. Don't ask an agent to
  wire things together.
- **Interface contracts prevent boundary bugs.** Most agent errors come from
  guessing an API. Spell out every signature in advance.
- **Tests catch what reviews miss; reviews catch what tests miss.** Run both.
- **Sequential on `main`, no worktrees.** Worktree isolation leaks commits; the
  serial cost is worth the simplicity.
