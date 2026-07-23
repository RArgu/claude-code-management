# Context Management

How this system keeps an AI agent's working context small, accurate, and
durable across a long-lived project.

## The documents

| File | Role | Lifespan |
|---|---|---|
| `CLAUDE.md` (per folder) | Map of one directory — its files, purpose, conventions, gotchas | Stable; updated when the folder changes |
| `TODO.md` | Volatile task queue — the only home for forward-looking state; each item is self-describing (a "done when", plus a few-line "context" block that lives on the task) | Rewritten continuously |
| `HANDOFF.md` | Session-to-session memory — backward-looking (the last 2-3 sessions); its newest entry also carries the next-session kickoff block | Rolling; old entries move to `session_logs/` |
| `DECISIONS.md` | Currently-in-force invariants only — loaded every session | Bounded (flat cap); supersede-in-place; retired entries move to `docs/decision-log.md`; changes need user approval |
| `docs/decision-log.md` | Superseded / obsolete decisions and their rationale | Append-only archive; never loaded at session start |
| `session_logs/session_NNN.md` | Per-session archive | Write-once; never loaded at session start |

Each document has exactly one job. `CLAUDE.md` answers *"where is X and how is
this folder organized."* `TODO.md` answers *"what's next."* `HANDOFF.md`
answers *"what just happened"* — and, in its newest entry, *"what to do first
next session."* `DECISIONS.md` answers *"what must not change."* Keeping the
jobs separate keeps each file short — and short files are cheap to load every
session. Two of the loaded files have a never-loaded cold-storage twin —
`HANDOFF.md` → `session_logs/`, and `DECISIONS.md` → `docs/decision-log.md` — so
the always-loaded copy stays small while the full history survives on disk.

## Hierarchical CLAUDE.md

Every directory carries a `CLAUDE.md` describing **only itself** — the files in
that folder, what each is for, local conventions, and gotchas. An agent
navigating the repo reads these maps top-down and opens a source file only when
it is ready to modify that specific file.

This replaces *exploratory* source-reading — opening files just to learn the
layout — which is the single largest avoidable context cost in a large repo.

### The halving cap

A `CLAUDE.md` at directory depth *n* is capped at **C / 2ⁿ** tokens, where C is
the root cap.

| Depth | Example | Cap (C = 5,000 tokens) |
|---|---|---|
| 0 | `CLAUDE.md` | 5,000 |
| 1 | `src/CLAUDE.md` | 2,500 |
| 2 | `src/strategy/CLAUDE.md` | 1,250 |
| 3 | `src/strategy/intraday/CLAUDE.md` | 625 |

Two properties make this work:

**1. Scope shrinks with depth.** The root must convey the whole project; a leaf
folder only its handful of files. The budget should shrink to match. A single
flat cap would either starve the root or let the leaves bloat.

**2. The cumulative cost is bounded.** Walking from root to a leaf, an agent
reads `C + C/2 + C/4 + …` — a geometric series that converges to **2C**.
However deep the tree grows, orienting along *any* root-to-leaf path costs at
most about twice the root cap. Context spent on navigation is effectively
constant in project size, not linear.

### The cap as a forcing function

When a `CLAUDE.md` outgrows its budget, the fix is never to raise the cap. It is to:

- promote detail *down* into a subfolder's `CLAUDE.md`,
- move a cross-cutting gotcha into `docs/`,
- or cut description that merely restates what a clear filename already says.

A map that is forced to fit its cap stays a map. A map allowed to grow without
limit becomes a second, prose copy of the code — and an unmaintained copy rots,
which is worse than no map at all.

`/housekeeping` checks every touched `CLAUDE.md` against its cap at the end of a
session; `/audit-docs` sweeps the whole tree periodically.

## Flat-capped documents

`TODO.md`, `HANDOFF.md`, and `DECISIONS.md` get flat caps — they are not
depth-scaled, because there is exactly one of each. A practical default is
~1,000 tokens each.

`HANDOFF.md` is the main lever. Keep only the last 2-3 sessions in it; move
older entries to `session_logs/`. Old context is not deleted — it is *demoted*
out of the always-loaded set, still on disk if a future session needs it.

`DECISIONS.md` works the same way, applied to policy instead of narrative: it
holds only the currently-in-force invariants, and when a decision is reversed or
the code it governed is removed, the old entry is demoted (with user approval)
to `docs/decision-log.md` — never loaded at session start, but kept for its
rationale. Without this, `DECISIONS.md` would grow append-only forever and
quietly inflate the cost of *every* session, since it loads at every start.
