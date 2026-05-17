# Claude Code Management

A workflow system for long-lived, multi-session software projects built with
[Claude Code](https://claude.com/claude-code). It keeps an AI coding agent
oriented, keeps its working context lean, and carries knowledge across
sessions — so session 50 starts as informed as session 2.

The system has three interlocking parts:

1. **Hierarchical `CLAUDE.md` maps** — every folder documents itself, under a
   token budget that halves with directory depth.
2. **Session-lifecycle skills** — a fixed open/close ritual for every session.
3. **Orchestration skills** — disciplined multi-agent build-test-review.

## The problem it solves

An AI agent working in a large repository burns context two ways:

- **Re-orienting** — reading source files just to learn how the project is laid
  out, every session, from scratch.
- **Re-learning** — rediscovering what the last session did, what was decided,
  and what broke, because none of it was written down.

Both costs grow with the project. This system holds them roughly constant.

## 1. Hierarchical CLAUDE.md maps

Every directory carries a `CLAUDE.md` that maps *only that directory* — its
files, their purpose, local conventions, and gotchas. An agent navigating the
repo reads these maps instead of source, and opens a file only when it is about
to change it.

The maps are kept honest by a token cap that **halves at each depth**:

| Depth | Example | Cap (root C = 5k tokens) |
|---|---|---|
| 0 | `CLAUDE.md` | 5,000 |
| 1 | `src/CLAUDE.md` | 2,500 |
| 2 | `src/strategy/CLAUDE.md` | 1,250 |
| n | … | C / 2ⁿ |

This has a useful property. The cumulative budget to read every map from the
root down to a leaf is a geometric series — `C + C/2 + C/4 + … = 2C`. **However
deep the tree grows, orienting along any path costs at most ~2× the root cap.**
The cap also acts as a forcing function: a map that cannot grow stays a map,
instead of decaying into a stale, prose copy of the code.

Full model and reasoning: [`docs/context-management.md`](docs/context-management.md).

## 2. Session lifecycle

Each session is wrapped in a fixed ritual. Full walkthrough:
[`docs/session-lifecycle.md`](docs/session-lifecycle.md).

| Skill | When | What it does |
|---|---|---|
| `session-protocol` | session start | Read decisions, draft fast, write to files, plan before implementing |
| `/housekeeping` | session end | Update TODO / HANDOFF / CLAUDE.md maps, write the session log, check budgets, commit |
| `/kickoff` | after housekeeping | Generate the next session's priming prompt |
| `/kickoff-critique` | after kickoff | Adversarially review that prompt before it is used |
| `/audit-docs` | periodically | Parallel agents sweep the doc tree for staleness |

## 3. Orchestration

For changes too large to make inline:

| Skill | For |
|---|---|
| `/orchestrate-mini` | A single task — plan, self-critique, execute, review |
| `/orchestrate` | A multi-module feature — an agent build-test-review cycle with explicit interface contracts |

`/orchestrate` encodes a hard-won rule: agents are reliable *inside* a single
module and unreliable *at* module boundaries — so the orchestrator defines every
interface contract up front and writes the integration code itself.

## Install

Copy the skills into any project:

```bash
cp commands/*           your-project/.claude/commands/
cp -r skills/*          your-project/.claude/skills/
```

Then seed the management docs from `templates/` — a root `CLAUDE.md`, a
`CLAUDE.md` in each folder, plus `TODO.md` and `HANDOFF.md`.

## Layout

```
commands/    Slash commands  → .claude/commands/
skills/      Agent skills    → .claude/skills/
docs/        The methodology — context management, session lifecycle
templates/   Starter CLAUDE.md / TODO.md / HANDOFF.md skeletons
```

## License

MIT — see [LICENSE](LICENSE).
