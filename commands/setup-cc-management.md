# /setup-cc-management — Install this workflow in a fresh repo

Run in a repo that has **no** management system yet. Installs the machinery,
generates the `CLAUDE.md` maps from the actual code, and seeds the lifecycle
docs. For a repo that already runs an older version, use `/modernize-cc-management`
instead. (Tell which: if `.claude/commands/` or a root `TODO.md`/`HANDOFF.md`
already exists → modernize; if not → this.)

Canonical source of truth:
`https://github.com/RArgu/claude-code-management` (branch `main`).

## Phase 0 — Safety
Create a branch (e.g. `setup-cc-management`). Work only there. Don't merge or
push — the user reviews first.

## Phase 1 — Install the machinery
Shallow-clone the source (`git clone --depth 1 <url> /tmp/ccm-src`). Copy into
this repo:
- `/tmp/ccm-src/commands/*` → `.claude/commands/`
- `/tmp/ccm-src/skills/*` → `.claude/skills/`

Read `/tmp/ccm-src/docs/context-management.md` and `docs/session-lifecycle.md`
so you understand the token-cap model and the open/close ritual before Phase 2.

## Phase 2 — Generate the CLAUDE.md maps (the real work)
Explore the repo and, using `/tmp/ccm-src/templates/CLAUDE.root.md` and
`CLAUDE.subfolder.md` as shape:
- Write a root `CLAUDE.md`: the project's goal, current shape, hard rules, and
  **pointers** to where detail lives — not a prose copy of the code.
- Write a `CLAUDE.md` in each significant folder mapping **only** that folder
  (its files, their purpose, local conventions, gotchas).
- Enforce the depth-halving caps: root ≤ ~5,000 tokens, each level deeper capped
  at half its parent (2,500 → 1,250 → …). A map that would exceed its cap means
  you're inlining detail that belongs in a `docs/` file — point instead. Maps
  load every session; keep them lean.

On a large repo, map folders in parallel (spawn `Explore`/agents per subtree)
rather than reading everything serially. Skip vendored/generated dirs
(`node_modules`, `dist`, `.venv`, …).

## Phase 3 — Seed the lifecycle docs from `/tmp/ccm-src/templates/`
- **`TODO.md`** — the single home for forward-looking state. Seed
  High-priority / Operational / Backlog with real items you found. Each item
  gets a `done when:` (deliverable / acceptance); give any item whose title
  hides detail a few-line `context:` block (constraints, environment, what's
  involved), and push oversized context into a `docs/` file the item points at.
- **`HANDOFF.md`** — backward-only (what happened). From the template. Add one
  bootstrap entry (Session 000) with a `### Next-session kickoff` block naming
  1–3 real starter priorities and the files to read, so the next session opens
  by just running `/kickoff-consume`. Do **not** add a forward "open items"
  section — forward state lives in `TODO.md`.
- **`DECISIONS.md`** — currently-in-force invariants only (seed any you can infer
  from the code / README; else leave the skeleton). Create an empty
  `docs/decision-log.md` archive from its template (the never-loaded tier that
  keeps `DECISIONS.md` bounded).
- Create an empty `session_logs/` directory (add a `.gitkeep`).

## Phase 4 — Verify & commit
- Confirm every `CLAUDE.md` sits within its depth cap and
  `TODO.md`/`HANDOFF.md`/`DECISIONS.md` within their ~1,000-token flat caps.
  Over budget → move detail to `docs/`, never raise a cap.
- Commit on the branch. Report: the map tree with each file's rough token count
  vs. its cap, the seeded `TODO.md` (with done-when / context), any seeded
  decisions, and the bootstrap kickoff. Don't merge or push — the user reviews.

After approval, the loop runs itself: each session starts with `/kickoff-consume`
and ends with `/housekeeping` (which chains `/kickoff`).
