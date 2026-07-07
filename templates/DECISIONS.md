# Decisions

<!-- Currently-in-force invariants ONLY. Loaded at every session start, so it
must stay lean — within ~1,000 tokens (flat cap). Over budget → supersede or
archive stale entries to docs/decision-log.md; never raise the cap.

Lifecycle: durable and APPROVAL-GATED. /housekeeping may PROPOSE adding,
superseding, or retiring an entry; the user approves. Entries never get deleted
outright — a superseded or obsolete decision moves to docs/decision-log.md
(append-only, never loaded, consulted on demand for rationale). This mirrors how
HANDOFF (loaded, capped) relates to session_logs (archived, uncapped). -->

## D-001 — <short title> — YYYY-MM-DD
<the invariant, stated as a rule that must not be contradicted without approval>

## D-002 — <short title> — YYYY-MM-DD
<...>

<!-- When a new decision reverses D-00X: replace D-00X's text here with the new
rule (keep a fresh id/date), and move the old text — plus why it was retired and
a pointer to the replacement — to docs/decision-log.md. When the code area a
decision governed no longer exists, move that entry to docs/decision-log.md with
an "obsolete: subject removed" note. Both require user approval. -->
