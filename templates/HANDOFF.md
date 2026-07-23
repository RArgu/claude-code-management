# Handoff

<!-- Session-to-session memory — BACKWARD-looking only (what happened). Newest
entry on top. Keep only the last 2-3 sessions here; move older entries to
session_logs/. Keep within ~1,000 tokens. Forward-looking state (what to do next)
lives in TODO.md, not here. Only the NEWEST entry carries a "Next-session kickoff"
block; older entries drop it when trimmed. -->

## Session NNN — YYYY-MM-DD

### Done
- <what was accomplished this session>

### Key decisions
- <decisions made — promote durable ones to DECISIONS.md>

### Next-session kickoff
<!-- Written by /kickoff (last step of /housekeeping); read by /kickoff-consume
at the start of the next session. Under 300 words. This is the ONLY carrier of
the kickoff — there is no separate scratch file. -->
```
Session NNN+1 kickoff. [N] priorities:

1. **[Top priority]** — [what, why, how]
2. **[Second priority]** — [...]

Key context from session NNN:
- [must-know context]

Read DECISIONS.md and [files relevant to the priorities] before starting.
Interview me on scope before proceeding.
```
