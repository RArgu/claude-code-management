# <Project Name>

<One-paragraph description: what this project is and what it does.>

## Goal

<What success looks like. Be concrete and measurable.>

## Identity

<What the agent is doing here, and the stakes — e.g. "You maintain a production
system; every change can affect real users. Validate before applying.">

## Code Standards

### P0 — Never break
1. No secrets in code — keys and credentials live in `.env` or a secrets store.
2. All external calls are wrapped with a timeout and explicit error handling.
3. No silent failure — every `except` logs, re-raises, or returns an explicit fallback.

### P1 — Standard practice
- Function and file size limits. Type hints on every signature.
- One source of truth for configuration; no magic numbers in business logic.
- KISS, DRY (extract on the 3rd duplicate), YAGNI.
- One-way imports — lower layers never import higher ones.

## Key References

- **@DECISIONS.md** — strategic invariants.
- **@docs/** — <pointers to the design docs that matter most.>

## Verification

<The commands that prove a change is sound — test suite, smoke checks, etc.>

## Housekeeping

Run `/housekeeping` at the end of every session.

<!-- Root CLAUDE.md. Depth 0 — cap = C (e.g. 5,000 tokens). This is the only
CLAUDE.md that carries project-wide goal, identity, and standards; subfolder
maps describe only their own directory. -->
