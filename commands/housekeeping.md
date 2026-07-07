# /housekeeping — End-of-Session Cleanup

Run at the end of every session. Complete all steps in order; the final step
chains into `/kickoff`, so this is the only close command the user types.

## 1. Update TODO.md
- Mark completed items `[x]`.
- Add items discovered during the session.
- Remove items that are no longer relevant.

## 2. Update HANDOFF.md
- Add a new session entry at the top: what was done, key decisions, open items.
- Keep only the 2-3 most recent sessions; trim older entries to `session_logs/`.
- Before trimming, scan for operational learnings that would be lost (see step 5).
- The upcoming priorities' file pointers go into the `### Next-session kickoff`
  block that `/kickoff` writes in step 8 — there is no separate "Next" note to
  maintain. Do NOT preload those pointers into `CLAUDE.md`.

## 3. Write the session log
- Create `session_logs/session_NNN.md` (next number): summary, work done,
  files created/modified, commit hashes.

## 4. Update the CLAUDE.md maps
- For every folder where files were created or significantly changed, update
  that folder's `CLAUDE.md` — add new entries, correct descriptions, remove
  stale references.
- A newly created folder needs a new `CLAUDE.md`.
- Keep every `CLAUDE.md` LEAN — it loads EVERY session, so it carries only general goals,
  hard rules, the project's current shape, a few itty-bitty recent-work lines, and
  **references** to where detail lives. Per-session history → `session_logs/` + `HANDOFF.md`;
  per-feature / as-built detail → the relevant `docs/` file. Note the general topic + point
  to the file; never inline the detail (it would load into context every session for nothing).

## 5. Promote learnings
Ask: *"What do I wish I'd known at the start of this session?"*

| Learning type | Destination |
|---|---|
| Strategic decision / invariant | `DECISIONS.md` (needs user approval) |
| Operational gotcha | the relevant folder's `CLAUDE.md`, or `docs/gotchas/` |
| Session-only detail | `session_logs/` |

When adding to `DECISIONS.md`: append the new invariant as its own entry with a
short id/date header. If the new decision **reverses or replaces** an existing
one, do NOT stack it on top — supersede in place (step 6).

## 6. Keep DECISIONS.md current and bounded
`DECISIONS.md` loads at every session start, so it must hold only the
**currently-in-force** invariants — not a growing pile of history. Review it
against what this session changed and propose, for user approval:

- **Superseded** — this session made a decision that reverses/replaces an
  existing entry: move the old entry's text (with the reason it was retired and
  a pointer to the replacement) to `docs/decision-log.md`, and leave only the
  new invariant in `DECISIONS.md`.
- **Obsolete** — the code area an entry governed no longer exists (a module was
  deleted/rewritten this session): move the entry to `docs/decision-log.md` with
  a note that its subject is gone.
- Deeper, code-wide obsolescence sweeps that span many sessions are the job of
  `/audit-docs`; step 6 only retires decisions tied to **this session's** changes.

Pruning an invariant, like adding one, **requires user approval** — present the
proposed supersede/retire list and never silently drop an invariant. Entries
only ever LEAVE `DECISIONS.md` by moving to `docs/decision-log.md` (append-only,
never loaded at start, consulted on demand for rationale) — nothing is deleted
outright.

## 7. Token-budget check
Every `CLAUDE.md` must sit within its depth cap — caps halve at each directory
level (see `docs/context-management.md`). `TODO.md`, `HANDOFF.md`, and
`DECISIONS.md` stay within their flat caps. If `DECISIONS.md` is over budget,
supersede or archive stale entries to `docs/decision-log.md` (step 6) — never
raise the cap. Same rule for the others: trim or promote detail elsewhere.

## 8. Commit
Stage and commit all housekeeping changes in a single commit:
```
git commit -m "Session NNN housekeeping: TODO, HANDOFF, session log, CLAUDE.md maps"
```

## 9. Generate the next kickoff
Run `/kickoff` now (do not wait for the user to ask). It reads the state you
just committed, hardens the kickoff through its built-in critique pass, and
writes it as the `### Next-session kickoff` block inside the newest `HANDOFF.md`
entry for the next session's `/kickoff-consume`. Kickoff runs AFTER the commit
so it always reads recorded, accurate state, then commits the kickoff block on
its own follow-up commit:
```
git commit -m "Session NNN kickoff for next session"
```
