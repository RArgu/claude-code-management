# /audit-docs — Documentation Audit Sweep

Periodically verify the documentation tree still matches reality. Launch
parallel Explore agents — one per concern — so the sweep stays fast and each
agent's context stays scoped.

## Agent 1: Root and top-level docs
- `CLAUDE.md` (root) — goal, code standards, key references, and verification
  commands still accurate? Every referenced file still exists?
- `TODO.md` — every item still actionable? Completed items checked off?
- `HANDOFF.md` — most recent entry matches the latest `session_logs/` entry?
- `DECISIONS.md` — holds only currently-in-force invariants, within its flat
  cap? Flag any entry whose governed code area no longer exists (deep
  obsolescence sweep) — it should be moved to `docs/decision-log.md` with user
  approval. The per-session version of this check lives in `/housekeeping` step 6.
- `docs/decision-log.md` — exists as the append-only archive; not loaded at start.

## Agent 2: The CLAUDE.md hierarchy
- Every folder has a `CLAUDE.md` — glob the directory tree, flag any folder
  missing one.
- Each `CLAUDE.md` is within its depth cap (root cap, halved per level).
- Each `CLAUDE.md` accurately lists the files actually in its folder — no
  missing files, no stale entries.

## Agent 3: The docs/ tree
- Every doc under `docs/` is reachable / indexed.
- Cross-references resolve.
- `Last Modified` dates roughly track the code they describe.

Each agent reports — file, issue, what is stale, what it should say — and does
not edit anything.

## After the agents complete
1. Summarize findings in one table: file | status (CLEAN / STALE) | issue.
2. Fix stale items directly (edit the `.md` files).
3. Commit: `git commit -m "Docs audit sweep: fix stale references"`.
