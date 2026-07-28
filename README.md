# Ledger-Driven Development

**LDD** — running a software project so that a durable, on-disk ledger, not a
chat transcript, is the memory. You describe the outcome like a product manager;
the agent does the engineering interrogation, records the decisions, and
decomposes the work into numbered tasks. Every task is then executable by a
**fresh session with zero prior context**.

Two Claude Code skills implement it.

## The skills

| Skill | Use when |
|---|---|
| `plan-initiative` | You describe something to build and no ledger exists yet. Interrogates → decision table → writes the folder. |
| `update-initiative` | A ledger exists and something changes: a new ask, a re-scope, a superseded decision, a promoted backlog item. Appends, never rewrites. |

## What a ledger looks like

```
.search-rework/
  PLAN.md              # architecture, decision table, phasing. Written once.
  DECISIONS.md         # append-only ADR log. Never edit past entries.
  STATE.md             # current task pointer + ledger + blockers + protocol
  REFERENCE.md         # codebase orientation, so no session spelunks twice
  MODELS.md            # which task deserves the expensive model
  EXECUTION_PROMPT.md  # pasteable prompt for a fresh session
  tasks/
    S01-index-schema.md
    S02-query-parser.md
    ...
```

Each task file carries the owner's ask **verbatim**, `file:line` anchors, checkbox
steps, a definition of done, and a **literal runnable verification command** — then
an empty `## Notes` the executing session fills in with what actually happened.

## The five rules

- **The ledger is the memory.** Nothing important lives in a chat transcript.
- **One session = one task.** Read STATE → read one task file → verify → commit → stop.
- **IDs are permanent addresses.** Never renumbered, never reused.
- **Decisions are append-only.** A superseded ADR still explains why the code
  looks the way it does.
- **Verification is a command, not a wish.** No task is done because it looks done.

## Install

```bash
git clone <this-repo> ~/ledger-driven-development
cp -R ~/ledger-driven-development/skills/* ~/.claude/skills/
```

Then in Claude Code: `/plan-initiative` to start one, `/update-initiative` to
change one. Both also trigger on their own from a plain product ask.

## License

MIT
