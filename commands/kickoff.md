# /kickoff — Next-Session Kickoff (generate → critique → persist)

Runs as the last step of `/housekeeping` (or standalone, to regenerate after a
plan change). Produces the kickoff for the **next** session, adversarially
hardens it, and writes it as a `### Next-session kickoff` block **inside the
newest `HANDOFF.md` entry** — the committed record the next session loads with
`/kickoff-consume`. No separate scratch file, no copy-paste.

## Steps

### 1. Read current state
`TODO.md`, `HANDOFF.md`, `DECISIONS.md`.

### 2. Draft the kickoff

```
Session NNN kickoff. [N] priorities:

1. **[Top priority]** — [1-2 sentences: what, why, how to approach]
2. **[Second priority]** — [1-2 sentences]
[... up to 3 priorities max]

Key context from session [N-1]:
- [Decision or change that affects how this session should work]
- [Bug, fix, or discovery the next session needs to know]
- [Process change or new tool available]
[... 3-5 bullets max, only things that would hurt to not know]

Read DECISIONS.md and [files relevant to the priorities] before starting.
Interview me on scope before proceeding.
```

Rules:
- Priorities come from `TODO.md` high-priority and operational items.
- Each priority's explaining context (what/why/how) is distilled from that task's
  `TODO.md` **context block** — TODO is the durable source; this kickoff is the
  digest. Session-level context (a decision or discovery that affects how the
  session works) comes from this session's `HANDOFF.md` entry (Done / Key decisions).
- Only include context that would change how the session operates — skip routine detail.
- **Actionable details must be traceable**: if a priority has known steps, reference
  which file holds them. If the steps aren't in `TODO.md`/`HANDOFF.md`, add them
  before generating the kickoff.
- **Name the files each priority needs to read** in the read-list. Source them from
  `TODO.md` and this session's HANDOFF entry — NOT from `CLAUDE.md` (kept lean of
  per-feature pointers). The read-list is executed verbatim by `/kickoff-consume` —
  do not list files it should not open. (There is no separate HANDOFF "Next" note to
  keep in sync — the read-list lives here, in the kickoff block.)
- Always end with "Interview me on scope before proceeding."
- Keep the whole kickoff block under 300 words.

### 3. Critique the draft
Evaluate it against these failure modes; list each issue found:
- **Priority ordering**: right sequence? unstated dependency? a 10-minute task
  inflated into a full priority?
- **Hidden scope**: work buried in "bonus" / "while we're at it" language —
  either it's in scope or it's not.
- **Missing references**: does every priority point to a specific `TODO.md`
  section or file? Can the next session find the steps without guessing?
- **Wasted words**: negative scope ("don't do X"), obvious context, restated
  `HANDOFF.md` content.
- **Dependency clarity**: sequential vs parallel — explicit either way?
- **Verifiability**: can someone tell when each priority is "done"?

### 4. Critique the criticism
For each issue: real problem or overcorrection? Would fixing it help the next
session, or is it pedantic? Does the fix kill useful flexibility? Pre-existing
or introduced? Mark each **valid**, **partially valid**, or **overcorrection**.

### 5. Rewrite
Incorporate only the valid and partially-valid critiques. Same format, same
rules, still under 300 words.

### 6. Sync references
For every file or section the rewrite cites: verify it exists and holds
actionable content; add steps where a reference is vague. (No separate "Next"
note to update — the read-list IS the forward pointer now.)

### 7. Write into HANDOFF
Write the final kickoff as a `### Next-session kickoff` block at the bottom of
**this session's (the newest) `HANDOFF.md` entry**:

```
## Session NNN — YYYY-MM-DD
### Done
...
### Key decisions
...
### Next-session kickoff
<the hardened kickoff text — for the session that starts with /kickoff-consume>
```

If a `### Next-session kickoff` block already exists in the newest entry (a
standalone rerun), replace it in place — the latest write wins; there is only
ever one pending kickoff, and it lives in the top entry. Commit it with the rest
of housekeeping (the block is committed, versioned `HANDOFF.md` content — no
scratch file, no `.gitignore`).

### 8. Report
Show the user the final kickoff text (fenced block) with critique verdicts and
commentary outside it, confirm it is now the `### Next-session kickoff` block of
the top `HANDOFF.md` entry, and remind them: next session starts with a single
command — `/kickoff-consume`.
