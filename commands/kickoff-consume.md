# /kickoff-consume — Start a Session from the HANDOFF Kickoff

Run as the FIRST command of a new session. Reads the `### Next-session kickoff`
block from the newest `HANDOFF.md` entry — written by the previous session's
`/kickoff` — primes context from it, and ends by interviewing the user on scope.
`session-protocol` governs behavior for the rest of the session. No scratch file,
no copy-paste.

## Steps

### 1. Load
Read `HANDOFF.md`. Take the newest (top) entry and find its `### Next-session
kickoff` block.

**If the top entry has no kickoff block** — the previous session didn't run
`/kickoff`, or this is a fresh project. Fall back: read `DECISIONS.md` and
`TODO.md`, propose priorities from the top HANDOFF entry's "Open items / next
session" and TODO high-priority items, and continue at step 4.

### 2. Check freshness
The kickoff block is stamped `Session NNN kickoff`. It is fresh iff it sits in
the **newest** HANDOFF entry AND that entry is session NNN−1 (one session behind
the kickoff it targets).

If a *newer* entry sits above the one carrying the kickoff, a session already
ran and superseded it — it is stale. Warn the user and offer: regenerate now
(run the `/kickoff` steps against current `TODO.md`/`HANDOFF.md`/`DECISIONS.md`,
then continue) or proceed knowingly. Never prime silently from a stale kickoff.

There is nothing to archive or rename: the kickoff is ordinary committed HANDOFF
content, and the next `/housekeeping` naturally demotes it by pushing a newer
entry on top.

### 3. Prime
Read `DECISIONS.md`, then every file named in the kickoff's read-list — and
nothing else. The kickoff already distilled `HANDOFF.md` and `TODO.md`; you have
the top HANDOFF entry from step 1, so do not re-read the rest of them here.

### 4. Confirm and interview
- Restate the priorities, one line each.
- Flag any recorded decision in `DECISIONS.md` that the priorities touch;
  never contradict one without user approval.
- Then **interview the user on scope before proceeding** — ask what is in and
  out for this session. Do no work until they answer.
