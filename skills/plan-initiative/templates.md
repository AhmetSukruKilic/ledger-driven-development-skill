# Initiative folder templates

Copy these skeletons. Replace `{…}`. Delete sections that don't apply — an empty
section is worse than a missing one. Both `plan-initiative` and
`update-initiative` use this file as the single source of file shape.

---

## `PLAN.md` — architecture. Written once.

```markdown
# {Initiative Name} — PLAN (Architecture)

Written once. Amend only via a new `DECISIONS.md` {ADR-PREFIX} entry referenced here.
Codebase orientation: `REFERENCE.md` (read that before touching any task).

## Goal

{2–4 sentences. What is true for a user when this ships that isn't today.}

## The invariant this initiative must not weaken

> {One sentence. The thing that must never break, even at the cost of features.}

{2–3 sentences on how this initiative respects it, and which parts of the system
it deliberately does not touch.}

## Topology

```
{ASCII diagram: services, who calls whom, which datastore, which external
dependency, which trust boundary. Keep it to what changed.}
```

## Decision table (full ADRs in DECISIONS.md)

| # | Decision | Chosen | Reason |
|---|----------|--------|--------|
| D1 | {the question} | {the pick} | {one clause} |

## Data model additions

{Migrations {NNNN}–{NNNN}. DDL summary or a pointer to it. Grants. What stays
unreachable from where.}

## {Domain-specific section}

{Metering, indexing strategy, retrieval pipeline, generation contract — whatever
this initiative's core mechanic is. One section, concrete.}

## Phasing / task clusters (see STATE.md ledger)

0. {Cluster name} ({ID}–{ID})
1. {Cluster name} ({ID}–{ID})

## Out of scope (post-{initiative})

{Explicit list. This is the scope fence — the most re-read section in the file.}
```

---

## `DECISIONS.md` — append-only ADR log.

```markdown
# {Initiative Name} — Decisions (append-only ADR log)

Never edit past entries. Supersede with a new dated entry referencing the one it
changes. Prefix `{ADR-PREFIX}` to avoid collision with {other ledgers in this repo}.
Referenced back into `PLAN.md`.

## {ADR-PREFIX}01 — {YYYY-MM-DD} — {Decision in a phrase}

**Context:** {The forcing question, and the options considered: (A) … (B) … (C) …}

**Decision:** {What was chosen, stated so a stranger could implement it.}

**Why not {rejected option}:** {The actual reason, not a platitude.}

**Consequences:** {What this costs, what it locks in, what stays unchanged.}
```

Superseding entry:

```markdown
## {ADR-PREFIX}09 — {YYYY-MM-DD} — {New decision} (supersedes {ADR-PREFIX}03)

**Context:** {What changed since {ADR-PREFIX}03 — measurement, cost, a shipped
constraint. Name it.}

**Decision:** …
**Consequences:** {ADR-PREFIX}03 no longer governs; older task Notes quoting it
were true when written.
```

---

## `STATE.md` — the only file every session reads in full.

Section order matters: protocol, pointer, environment, blockers, ledger, backlog.

```markdown
# {Initiative Name} — State

Last updated: {YYYY-MM-DD}
Last session ended: **{ID} done ({model used}).** {A fat paragraph: what actually
landed, which files, what surprised you, the literal verification output
(`N passed`), what the next task must know. Keep prior entries below as
`Prior: …` — this is the project's narrative memory.}

Prior: **{ID} done.** {…}

## Execution protocol (follow exactly)

Read this file → read `REFERENCE.md` once → read only the current task's file →
check `MODELS.md` for the recommended model → do the work, ticking checkboxes →
run the task's `## Verification` command verbatim → fill in the task's `## Notes`
→ update this file's ledger row, "Current task" pointer, and "Last session ended"
line → commit as `{ID}: <title>` → **STOP. Do not roll into the next task.**

## Current task

**{ID} — {title}** ({repo}, {model}).
{2–4 sentences: why this one is next, what it depends on that is already done,
and the one trap in it.}

## Environment

{What is already set up (venv, docker, ports, credentials) and what a session
must start itself. Include the literal commands.}

## Open blockers / decisions for the user

{Things only the owner can do: buy a domain, create a SKU, run an operator SQL
statement, approve a spend. Name which task each unblocks. Delete when resolved —
this section should usually be empty.}

## Task ledger ({FIRST}–{LAST})

Statuses: todo → in_progress → done → (blocked if waiting on user).
`Repo`: blank = this repo; `{other}` = `{other-repo}`.

| ID | Title | Repo | Status | Depends on |
|----|-------|------|--------|------------|
| {ID} | {title} | | todo | — |

## Critical path

{The subsequence that unblocks everything else, one line.}

## Backlog (deferred, unnumbered — promote to a task when its trigger fires)

- **{Thing}** — {why deferred, and the literal trigger that promotes it.}
```

---

## `tasks/{ID}-{slug}.md` — one per task. The unit of work.

```markdown
# {ID} — {Title}
REPO: {repo} · Depends: {IDs or —} · Status: {todo|in_progress|done|blocked}
Read first: STATE.md, REFERENCE.md, then this.
**Model: {tier}** — {one clause on why this tier}.

## Goal
Owner's ask:

> {The user's own words, verbatim. Never paraphrase this quote — it is the
> contract, and paraphrase is how scope drifts.}

{2–3 sentences translating it into this task's slice. Name the sibling task that
owns the other half, if any.}

## Before / After
State the change from outside the code — what someone using or calling the system
sees. If the "After" column can only be written as an implementation detail, the
task is not specified yet.

| | Today | After this task |
|---|---|---|
| {what the user / caller does} | {current behaviour, concrete} | {new behaviour, concrete} |
| {the shape of the data or reply} | {what it holds now} | {what it holds then} |
| {the failure this closes} | {what happens today} | {what happens instead} |

**Unchanged on purpose:** {the thing a reviewer might expect to move and must not
— an interface, a wire format, a file, an exit code. Name it, or write "nothing".}

## {Security boundaries | Non-negotiables}
- {Constraint stated as a prohibition, with the reason.}
- {Include the attack surface / failure mode this task is closest to.}

## Context (anchors)
- `path/to/file.py` — {what lives here and why this task cares}.
- `path/to/other.py`
  - `function_name` :{line} — {what it does today}.
  - {The trap: three call sites, one rule; or "if {other task} landed, there are
    four — grep for `{symbol}` and fix every one.}

## Steps
- [ ] {One action. Concrete enough to do without a decision.}
- [ ] {…}
- [ ] Tests: {each case, named, including the negative case that proves the
      change is what caused the outcome.}

## Definition of done
- {Observable outcome, stated from outside the code.}
- {The thing that must remain byte-for-byte unchanged.}

## Verification
`{literal runnable command}`

{Then live: the manual steps, in order, with expected results. Include cleanup.}

## Notes

{Empty until the task is done. The executing session fills this with: what
actually happened, every deviation from the plan, the verification output
verbatim, what was deliberately NOT done and why, and a "For {next task}"
hand-off paragraph.}

{If reality differed from `## Before / After`, say so here in one line and leave
that table as written — it is what was planned, and the difference is the finding.
A measurement that contradicts it is an ADR, not an edit.}
```

---

## `REFERENCE.md` — codebase orientation. Written once, patched when stale.

Only when a codebase already exists. Purpose: no session ever spelunks twice.

```markdown
# {Initiative Name} — REFERENCE (read this once, then you don't need to spelunk)

Single orientation doc for any agent executing a task. Verified against the
codebase as of {YYYY-MM-DD} ({migration head / commit}). If reality diverges,
trust the code and fix this file.

## Services, ports, roles

| Service | Package | Port | DB role | Trust |
|---|---|---|---|---|

## Commands

```bash
{the literal commands: setup, migrate, test, smoke}
```

## HTTP contracts

{Endpoint → request → response shape, for the surfaces tasks touch.}

## Key code anchors

{`path:symbol` → one line on what it does. The 10–20 places tasks will land.}

## Schema

{Tables this initiative reads or writes, and the grants.}

## Conventions

{Naming, error handling, test layout, migration rules — the things a reviewer
would flag.}
```

---

## `MODELS.md` — model per task. Only when the user budgets models.

```markdown
# {Initiative Name} — Recommended Model Per Task

{The budget reality in one sentence.}

| ID | Title | Model | Why |
|----|-------|-------|-----|
| {ID} | {title} | {tier} | {one clause} |

## Summary

- **{Expensive tier} ({n} tasks):** {IDs}
- **{Cheap tier} (rest):** everything else

Rule of thumb: **{the domain's own rule — e.g. auth + money + safety-invariant =
expensive; schema + UI + config = cheap}.** When unsure on a cheap-tier task, run
it cheap then code-review the diff — cheaper than running the whole task expensive.
```

---

## `EXECUTION_PROMPT.md` — pasteable prompt for a fresh session.

````markdown
# {Initiative Name} — Execution Prompt

Paste this verbatim as the prompt for each new session working the `.{slug}/`
ledger. One session = one task. The session has no memory of prior sessions —
everything needed lives in these files.

---

## Prompt (copy from here down)

You are executing one task from the {Initiative Name} ledger in `.{slug}/`.
Follow this protocol exactly, in order. Do not skip steps, do not batch multiple
tasks, do not improvise scope beyond the task file.

1. **Read `.{slug}/STATE.md` in full** — ledger, statuses, and the "Current task"
   pointer.
2. **Pick the task:**
   - If "Current task" names one, use it — unless the user's message this session
     names a different ID, in which case the user wins.
   - Otherwise take the first `todo` row whose `Depends on` is empty or all-`done`.
     Ties go to the earlier row (the table is dependency-sorted).
   - If the `Repo` column names another repo, confirm you're in it before doing
     anything else.
   - If nothing is eligible, stop and report — don't invent work.
3. **Name the terminal** the moment the task is picked: `{ID} — <3–5 word
   description>` (e.g. `P04 — refund webhook retries`). The Bash tool has no
   `/dev/tty`, so write the title to the parent `claude` process's tty:
   ```bash
   p=$$; while t=$(ps -o tty= -p $p | tr -d ' '); [ -z "$t" ] || [ "$t" = "??" ]; do p=$(ps -o ppid= -p $p | tr -d ' '); done
   printf '\033]0;%s\007' "P04 — refund webhook retries" > "/dev/$t"
   ```
   Then always ask the user to run `/rename {ID} — <description>` too, and go on.
   A clean exit proves nothing: the `printf` succeeds even when the title
   renders nowhere — an editor terminal that ignores OSC titles, or a session
   running in an IDE panel with no terminal tab. Never report the terminal named.
4. **Read `.{slug}/REFERENCE.md` once.** Trust it; patch it only if stale.
5. **Read only the current task's file.** Other task files belong to other sessions.
6. **Check `.{slug}/MODELS.md`** for this task's recommended model. If it's flagged
   for the expensive tier and you aren't running it, say so before proceeding.
7. **Do the work.** Tick each `## Steps` checkbox as you go. Stay in scope — note
   adjacent work in the Backlog, don't fold it in.
8. **Run the `## Verification` command exactly as written.** Don't claim done
   without seeing it pass. If it fails, fix the code — never the command.
9. **Mark it done:** fill the task file's `## Notes`; flip the STATE.md ledger row
   to `done`; repoint "Current task"; rewrite "Last session ended".
10. **Commit** as `{ID}: <title>`, including the `.{slug}/` file changes.
11. **STOP.** The next task is the next session's job.

### If blocked mid-task

Set the row to `blocked`, write the blocker into STATE.md's "Open blockers"
section (what's needed, which tasks it unblocks), and stop. Don't guess at
credentials, SKUs, or external config the owner owns.

### Guardrails that apply regardless of task

- **Never write to the agent's local memory** (`~/.claude/projects/*/memory/`,
  `MEMORY.md`) — no creating, editing or deleting a memory file. **Hard rule.** If
  something seems worth remembering, say so to the user and write it only after
  they approve. What the next session needs goes in the ledger, not in memory.
- {The invariant, restated as a stop condition.}
- {Repo-specific rules: migration policy, grant policy, machine limits.}

---

## Why this file exists

`STATE.md`'s "Execution protocol" is the source of truth (keep them in sync).
This file is the same protocol shaped as a standalone pasteable prompt, and it
front-loads task *selection*, which STATE.md assumes you already know.
````
