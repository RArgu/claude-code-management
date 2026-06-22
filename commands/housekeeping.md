# /housekeeping — End-of-Session Cleanup

Run at the end of every session. Complete all steps before committing.

## 1. Update TODO.md
- Mark completed items `[x]`.
- Add items discovered during the session.
- Remove items that are no longer relevant.

## 2. Update HANDOFF.md
- Add a new session entry at the top: what was done, key decisions, open items.
- Keep only the 2-3 most recent sessions; trim older entries to `session_logs/`.
- Before trimming, scan for operational learnings that would be lost (see step 5).
- End the entry with a **"Next"** note that NAMES the specific files the upcoming
  priorities will need to read. These per-feature file pointers live in HANDOFF (where
  the next session and `/kickoff` look for them) — do NOT preload them into `CLAUDE.md`.

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

## 6. Token-budget check
Every `CLAUDE.md` must sit within its depth cap — caps halve at each directory
level (see `docs/context-management.md`). `TODO.md` and `HANDOFF.md` stay within
their flat caps. If a file is over budget, trim it or promote detail elsewhere —
never raise the cap.

## 7. Commit
Stage and commit all housekeeping changes in a single commit:
```
git commit -m "Session NNN housekeeping: TODO, HANDOFF, session log, CLAUDE.md maps"
```
