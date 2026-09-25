# TASK.md — Session Workflow Definition

Part of the shared Claude Code workflow repo, alongside `WORKFLOW.md` (which points
here for the Session Boot Sequence rather than duplicating it — see its Session Loading
Strategy section). A connected project's local `CLAUDE.md` holds the path to this repo
and `@import`s `WORKFLOW.md`; this file is read on cue, not auto-loaded.

This document defines **how** sessions operate. It is not a task descriptor.
Update it only when the workflow itself changes.

---

## Session Boot Sequence

1. The project's local `CLAUDE.md` is auto-loaded by the shell, which `@import`s `WORKFLOW.md`.
2. AI reads this file (`TASK.md`) to learn the rules of engagement.
3. AI reads `docs/index.md` — always, unconditionally. It's the master index of every
   doc file and its one-line summary; use it to decide what else needs loading. Never skip it.
4. **(Optional)** User may provide a **task working directory** path (anywhere on disk, not committed).
   - If provided: AI locates `<task-name>.md` in that directory and resumes from it.
     If absent, AI creates it from any task description files found in the dir.
   - If not provided at session start: assume none exists. Do **not** ask for one upfront.
     `MEMORY.md` + the **1–2 most recent** patch notes are sufficient for most sessions —
     never load more than 2 upfront.
   - AI may ask for one **mid-session** if the task grows complex enough to warrant it
     (many open decisions, multi-group state, cross-session tracking needed).
5. Read `memory/MEMORY.md`'s **Next Session Entry Point** — this is the authoritative
   answer to "what's next." Do **not** check version-control log/status as a default
   orientation step: commits only happen after the full doc/memory audit at session end
   (see Task Completion), so by the time a new session starts, prior work is already
   committed and memory reflects it. Only inspect that state if a concrete step in the
   session requires it, or the user asks.
6. All other docs (`PROGRESS.md`, module docs) and all source/interface/test files are loaded
   **on demand only** — never upfront, never "for the full picture." Load a doc only when
   `index.md` or the concrete next step indicates it's relevant. This is a standing
   discipline for the *whole* session, not just boot.
7. `docs/FILES_INDEX.md` gets the same on-demand treatment as `index.md`, one level down:
   never loaded at boot, but **mandatory the moment a code file is needed** — read it
   before opening any source/interface/test file. It often answers the question without
   opening the file at all.

---

## Session Context Limit

When context approaches full (visible via `/context` — typically >80% used):

1. **Update `memory/MEMORY.md`** — write the current session state so the next session can resume cold:
   - Update "Project State" date and status.
   - Rewrite "Next Session Entry Point" with exactly what to do first, what's in progress, and any open decisions or hypotheses.
   - Capture any discovered invariants, formulas, or domain behaviors that would otherwise require re-investigation.
2. **Tell the user** the memory is updated and suggest clearing the session (`/clear`) and restarting.
3. **Do not wait** for autocompact — it discards conversation history non-deterministically. Proactive save is always safer.

The fresh session loads `MEMORY.md` and resumes without loss of context.

---

## Task Working Directory

- User-created folder, location is arbitrary and not committed to the repo.
- May contain: task description, screenshots, HTML references, JSONs, notes, etc.
- AI may freely create and maintain any files inside it:
  context notes, summaries, analyses, diffs, todos, caches, logs, scratch files.
- No restrictions on file count or type inside this directory.

### Task Tracker File (`<task-name>.md`)

AI creates and maintains one tracker file per task, named after the task identity
(not the date). This file is the **idempotent session state** — any session can
resume from it.

Required content:
- Task name, goal, status, completion criteria
- Running decisions and their rationale
- Todo list (open and completed items)
- Progress notes (timestamped entries encouraged)
- Links to relevant `docs/` entries

---

## context_dir/ — AI Scratch & Ideas (Uncommitted)

- Lives at the repo root, gitignored except `.gitkeep`. Distinct from both the **task working
  directory** (user-provided, external to the repo, per-task) and `docs/` (committed,
  AI-maintained project documentation).
- AI's own playground for ideas, future decisions, or notes that aren't ready to commit and
  don't fit the `docs/` tree — e.g. speculative architecture ideas unrelated to current scope.
- AI may create and organize files here freely, without asking each time.

---

## Resuming an Interrupted Session

1. Follow boot sequence above.
2. User provides the same working directory path.
3. AI locates `<task-name>.md` → reads it → resumes without loss of context.

---

## Commit Phrasing

**"Prepare for a commit"** or **"prepare a commit"** means: update docs, run the doc audit, stage
the relevant files, and show the user what would be committed (a summary of changes). It does **NOT**
mean execute a commit. The commit is only created when the user explicitly says "commit", "commit
now", "go ahead and commit", or equivalent. Never auto-commit when asked to prepare.

---

## Task Completion

When the user signals a task is done:

1. AI appends one entry to `docs/PROGRESS.md`:
   ```
   ## [YYYY-MM-DD] <task-name>
   <one-line summary>[ — see patch_notes/YYYY-MM-DD_<name>.md]
   ```
   *(patch note link included only if one was created)*
1.5. **Doc audit** — run this whenever staging a commit at task end *or* when the user explicitly
   asks to update docs. Use `docs/index.md` as the master list of all doc files and their
   summaries (`docs/FILES_INDEX.md` indexes project files — code, interfaces, tests — not docs),
   then open and scan each doc that could be stale against what changed:
   - `docs/index.md` — update any summary lines whose doc content changed this session
   - `docs/ARCHITECTURE.md` (or project-equivalent) — module status table, grouping status, validation-suite passing list
   - `docs/modules/<affected>.md` — grouping tables, test coverage rows, status lines
   - `docs/FILES_INDEX.md` — rows for any added/moved/renamed files
   - `docs/PROGRESS.md` — append one entry for the completed task (append-only; never skip)
   - `memory/MEMORY.md` — **must be cold-start-ready before commit:** a session opening it fresh should know exactly what to do next and how to start, without asking. Project state date current; next entry point updated; completion status current; anything that would otherwise require re-investigation captured.
   - `memory/MEMORY.md` **structure** — cross-check against the auto-memory system's own rules (index stays thin, one-line entries, detail lives in separate topic files, total length within its line budget) and against `CLAUDE.md`/`WORKFLOW.md`/`TASK.md` wording, to catch drift before it causes truncation or a stale cold-start. Durable codebase facts and decisions belong in `docs/` (an existing module doc, or a new AI-owned doc if none fits — ask if placement is ambiguous); memory holds only session-continuity state and durable agreements between the user and AI.
   - **Kit-sourced memories** (those with `metadata.kitTemplate`) — compare each one's `kitVersion`
     with the same key in `$setupRepo/<kitTemplate>`. If any is behind, tell the user once that
     `/workflow-update` is due. Report only; never edit the memory yourself — a difference from the
     kit may be a deliberate project override.
   Also ask: does anything from this task belong in a *different* module doc?
   **If placement is ambiguous** (new module doc vs. existing, or which module owns a concept) — ask the user.
   Doc updates travel in the same commit as the task they belong to. If the session is doc-only, they get their own commit.
2. **Patch note** — AI creates `docs/patch_notes/YYYY-MM-DD_<name>.md` **only when the user
   explicitly asks**. Covers the full logical task across all its commits. See `WORKFLOW.md`'s
   patch_notes rules for boundary detection, consolidation, and the 4-file active cap.
3. The task working directory and tracker file are left in place (user manages cleanup).

---

## Multi-Developer Notes

- `TASK.md` (this file) does not change during active work — no conflicts.
- `docs/PROGRESS.md` is append-only — concurrent appends do not conflict.
- Task working directories are personal/local — not committed — no conflicts.
- `docs/patch_notes/` entries use unique date+name filenames — no conflicts.
