# Session Lifecycle

A *session* is one continuous conversation with the agent. The system wraps
every session in a fixed open/close ritual, so context is primed at the start
and captured at the end.

## The loop

```
 /kickoff-consume → session-protocol → work → /housekeeping ──(runs)──► /kickoff
        ▲                                                                    │
        │   reads the kickoff from the           writes the kickoff into the │
        │   top HANDOFF.md entry                  top HANDOFF.md entry        │
        └──────────────────────────── next session ◀─────────────────────────┘
```

## Start

The user's first message is a single command: **`/kickoff-consume`**. It reads
the `### Next-session kickoff` block from the newest `HANDOFF.md` entry (written
by the previous session's `/kickoff`), checks it is fresh (the top entry is one
session behind), reads `DECISIONS.md` and the kickoff's read-list — and nothing
else — then restates the priorities and interviews the user on scope before any
work. There is no pasted prompt and no scratch file; the kickoff is ordinary
committed `HANDOFF.md` content. If the top entry has no kickoff block (a
kickoff-less or fresh-project session), consume falls back to priming from
`DECISIONS.md` and `TODO.md`.

**`session-protocol`** governs behavior for the rest of the session: produce a
first draft within a few tool calls instead of over-exploring, write artifacts
to files rather than chat, and stay plan-only unless implementation was
requested. (It reads `DECISIONS.md` itself only when the session was *not*
opened by `/kickoff-consume`, which already loaded it — no double read.)

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

One command the user types: **`/housekeeping`**. It:

1. Updates `TODO.md`, `HANDOFF.md`, and the `CLAUDE.md` maps for every touched
   folder; writes the session log; checks token budgets; commits.
2. Keeps `DECISIONS.md` bounded — it loads every session, so housekeeping
   proposes (approval-gated) moving any superseded or obsolete invariant to
   `docs/decision-log.md`, the never-loaded archive tier. Nothing is silently
   dropped; entries leave only by moving to the log.
3. As its final step, runs **`/kickoff`** — which generates the next session's
   kickoff, folds in the old adversarial critique (draft → criticize →
   counter-criticize → rewrite), syncs references, and writes the hardened
   kickoff *into* the newest `HANDOFF.md` entry (not a separate file).

The order is now enforced structurally rather than by convention: kickoff runs
inside housekeeping, after the commit, so it always reads freshly-recorded
state. The separate `/kickoff-critique` command is retired — its pass lives
inside `/kickoff`.

## Periodically

**`/audit-docs`** — independent of any single session, run a parallel-agent
sweep that checks the whole documentation tree against the actual code and
flags staleness.
