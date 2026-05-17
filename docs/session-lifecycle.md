# Session Lifecycle

A *session* is one continuous conversation with the agent. The system wraps
every session in a fixed open/close ritual, so context is primed at the start
and captured at the end.

## The loop

```
 [kickoff prompt] → session-protocol → work → /housekeeping → /kickoff → /kickoff-critique
        ▲                                                                        │
        └──────────────────────────── next session ◀────────────────────────────┘
```

## Start

**`session-protocol`** (a skill applied at the first message): read
`DECISIONS.md`, produce a first draft within a few tool calls instead of
over-exploring, write artifacts to files rather than chat, and stay plan-only
unless implementation was requested.

The **kickoff prompt** — generated at the end of the *previous* session — is
the user's first message. It names the priorities, the must-know context, and
the files to read before starting.

## Work

Whatever the session is for. One rule carries through all of it: **artifacts go
to files, not chat.** A plan or analysis written only into the conversation is
lost at the next context compression. A file survives.

For changes too big to make inline, use the orchestration skills:

- **`/orchestrate-mini`** — plan → self-critique → execute → review, for a
  single-file or single-task change.
- **`/orchestrate`** — full multi-agent build-test-review with explicit
  interface contracts, for a multi-module feature.

## Close

Three skills, run in order:

1. **`/housekeeping`** — update `TODO.md`, `HANDOFF.md`, and the `CLAUDE.md`
   maps for every touched folder; write the session log; check token budgets;
   commit.
2. **`/kickoff`** — generate the next session's kickoff prompt from the
   freshly-updated `TODO.md` and `HANDOFF.md`.
3. **`/kickoff-critique`** — adversarially review that kickoff (criticize,
   counter-criticize, rewrite, sync references) before the user copies it.

The order matters: housekeeping makes the recorded state accurate, kickoff
reads that state, and kickoff-critique hardens the result.

## Periodically

**`/audit-docs`** — independent of any single session, run a parallel-agent
sweep that checks the whole documentation tree against the actual code and
flags staleness.
