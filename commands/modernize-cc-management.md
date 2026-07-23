# /modernize-cc-management — Upgrade an existing install to the current design

Run in a repo that **already** runs an older version of this workflow. Refreshes
the skills and migrates the data-file conventions to current. For a repo with no
management system yet, use `/setup-cc-management` instead. (Tell which: if
`.claude/commands/` or a root `TODO.md`/`HANDOFF.md` already exists → this; if
not → setup.)

Canonical source of truth:
`https://github.com/RArgu/claude-code-management` (branch `main`).

## The design you're migrating TO
- The next-session kickoff is a `### Next-session kickoff` block inside the
  newest `HANDOFF.md` entry (not a scratch file, not copy-pasted). The next
  session opens with a single `/kickoff-consume`; the close is one command,
  `/housekeeping`, which runs `/kickoff` as its last step. `/kickoff-critique`
  is retired (its adversarial pass folds into `/kickoff`).
- `DECISIONS.md` holds **only** currently-in-force invariants and loads every
  session; superseded / obsolete ones move to a never-loaded
  `docs/decision-log.md` archive (approval-gated).
- `TODO.md` is the **single** home for forward-looking state. Every item has a
  `done when:` (deliverable / acceptance) and may carry a few-line `context:`
  block — the constraints / environment / what's-involved the one-line title
  hides — which lives **on the task** so it survives session trimming (overflow
  → a `docs/` file the item points at). `HANDOFF.md` is **backward-only** (what
  happened); its old "Open items / next session" section is removed.

## Phase 0 — Safety
Create a branch (e.g. `modernize-cc-management`). Never work on the default
branch. Do not commit until the user approves Phase 2.

## Phase 1 — Refresh the skills (mechanical)
Shallow-clone the source (`git clone --depth 1 <url> /tmp/ccm-src`). Then, into
this repo's `.claude/`:
- Overwrite `commands/housekeeping.md`, `commands/kickoff.md`,
  `commands/audit-docs.md`
- Add `commands/kickoff-consume.md` (and, optionally, `commands/setup-cc-management.md`
  + `commands/modernize-cc-management.md` so future upgrades are one command)
- Overwrite `skills/session-protocol/SKILL.md`
- **Delete** `skills/kickoff-critique/` (folded into `/kickoff`)

Before overwriting, diff each existing file against the source. If any were
locally customized for this project, show the user the diff and re-apply those
customizations on top of the new version — don't silently clobber them.

## Phase 2 — Migrate the data files (judgment — needs user approval before writing)
- **`DECISIONS.md`**: likely append-only today. Propose a split — keep only the
  currently-in-force invariants in `DECISIONS.md`, move every superseded /
  obsolete entry (with rationale + a pointer to its replacement) into a new
  `docs/decision-log.md`. Present the in-force set vs. the archive set as a table
  and **wait for approval** before moving anything. Copy the
  `templates/DECISIONS.md` and `templates/decision-log.md` headers from source.
- **`TODO.md`**: reformat existing items to the new convention — add a
  `done when:` to each, and a `context:` block to any item whose title hides
  detail (especially near-term ones); push oversized context into a `docs/` file
  the item points at. Preserve every existing item; don't invent deliverables you
  can't infer — flag those for the user to fill in.
- **`HANDOFF.md`**: if it has an "Open items / next session" (or similar forward)
  section, migrate those entries into `TODO.md` as items (with done-when +
  context), then remove the section. Leave the backward Done / Key-decisions
  history as-is; the next `/kickoff` adds the kickoff block — don't hand-author one.
- Clean up any pass-1 remnants if present: delete `.claude/next-kickoff.md` and
  `.claude/last-kickoff.md`, and remove any `".claude/*-kickoff.md"` line from
  `.gitignore`.

## Phase 3 — Verify & consistency-sweep
- `grep` every `*.md` in the repo (`CLAUDE.md` maps, README, docs) for stale
  references: `kickoff-critique`, `next-kickoff`/`last-kickoff`,
  `copy-paste`/`paste the kickoff`, `Open items / next session`,
  `DECISIONS ... append-only`, the old 3-command close. Update any real hits so
  the repo's own docs match.
- Confirm `DECISIONS.md`, `TODO.md`, `HANDOFF.md` each fit their ~1,000-token
  flat caps and every `CLAUDE.md` fits its depth cap. Over budget → archive /
  point to `docs/`, never raise a cap.

## Phase 4 — Report
Commit on the branch and report: the file-by-file diff summary, anything
preserved as a local customization, the `DECISIONS.md` split awaiting sign-off,
and any `TODO.md` items needing a deliverable the user must fill in. Do not merge
or push — the user reviews first.
