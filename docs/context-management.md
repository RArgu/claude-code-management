# Context Management

How this system keeps an AI agent's working context small, accurate, and
durable across a long-lived project.

## The documents

| File | Role | Lifespan |
|---|---|---|
| `CLAUDE.md` (per folder) | Map of one directory — its files, purpose, conventions, gotchas | Stable; updated when the folder changes |
| `TODO.md` | Volatile task queue | Rewritten continuously |
| `HANDOFF.md` | Session-to-session memory — the last 2-3 sessions | Rolling; old entries move to `session_logs/` |
| `DECISIONS.md` | Durable invariants and strategic choices | Append-only; changes need user approval |
| `session_logs/session_NNN.md` | Per-session archive | Write-once |

Each document has exactly one job. `CLAUDE.md` answers *"where is X and how is
this folder organized."* `TODO.md` answers *"what's next."* `HANDOFF.md`
answers *"what just happened."* `DECISIONS.md` answers *"what must not change."*
Keeping the jobs separate keeps each file short — and short files are cheap to
load every session.

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

`TODO.md` and `HANDOFF.md` get flat caps — they are not depth-scaled, because
there is exactly one of each. A practical default is ~1,000 tokens each.

`HANDOFF.md` is the main lever. Keep only the last 2-3 sessions in it; move
older entries to `session_logs/`. Old context is not deleted — it is *demoted*
out of the always-loaded set, still on disk if a future session needs it.
