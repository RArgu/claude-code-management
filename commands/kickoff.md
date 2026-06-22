# /kickoff — Next-Session Kickoff Generator

Run at the end of a session (after `/housekeeping`) to generate a kickoff
message for the **next** session. The output is a copy-pasteable prompt the
user feeds as the first message of a fresh conversation, priming it with
priorities, context, and instructions.

## Steps

1. **Read current state**: `TODO.md`, `HANDOFF.md`, `DECISIONS.md`.
2. **Generate the kickoff message** with these four sections:

### Format

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

Read DECISIONS.md, HANDOFF.md, and [files relevant to the priorities] before starting.
Interview me on scope before proceeding.
```

### Rules

- Priorities come from `TODO.md` high-priority and operational items.
- Key context comes from the most recent `HANDOFF.md` entry.
- Only include context that would change how the session operates — skip routine detail.
- **Actionable details must be traceable**: if a priority has known steps, reference
  which file holds them. If the steps aren't in `TODO.md`/`HANDOFF.md`, add them
  before generating the kickoff.
- **Name the files each priority needs to read.** Source the read-list ("files relevant
  to the priorities") from `HANDOFF.md`'s "Next" note and `TODO.md` — NOT from `CLAUDE.md`
  (kept lean of per-feature pointers). If those files aren't named there yet, add them to
  `HANDOFF.md` first, then cite them in the kickoff.
- Always end with "Interview me on scope before proceeding."
- Keep the whole message under 300 words.

3. **Present the message** to the user for review before they copy it.
