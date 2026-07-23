# TODO

## High priority
- [ ] <item> — done when: <deliverable / acceptance>
      context:
      - <a few lines the title hides: constraint, environment, what's involved>

## Operational
- [ ] <item> — done when: <deliverable / acceptance>

## Backlog
- [ ] <item> — done when: <deliverable / acceptance>
      context: <one line, or `see docs/<file>.md` when it's more than a few lines>

<!-- Volatile task queue and the ONLY home for forward-looking state (HANDOFF is
backward-only). Mark done with [x]; remove resolved items after a session or two.
Keep within ~1,000 tokens — if it grows past that, the backlog is the first thing
to prune.

Item rules:
- Every item carries a one-line "done when" (deliverable / acceptance). No
  deliverable → it's a thought, not an item.
- Every item MAY carry a "context" block — a few lines explaining what the title
  hides (design constraints, environment, what's involved), so any session that
  pulls the task into scope inherits it without re-deriving it. The context lives
  ON the task (not in HANDOFF), so it survives as long as the task does. Add it
  whenever useful; make sure a near-term / in-scope item has it, and that any
  item CARRIED FORWARD unfinished carries at least a one-line context — captured
  while it's still fresh, before the spawning session trims to session_logs/.
  Throwaway backlog jots may stay terse until they surface.
- If an item's context outgrows a few lines, put it in a docs/ file and have the
  item point to it (`context: see docs/<file>.md`) — never inline a wall here.
- `/kickoff` reads these context blocks and distills the in-scope ones into the
  next session's kickoff; TODO is the durable source, the kickoff is the digest. -->
