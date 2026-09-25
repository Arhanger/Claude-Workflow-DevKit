# WORKFLOW.md — Shared Session & Documentation Workflow

This file is the language-agnostic core of a Claude Code project workflow. It is imported
by a project's local `CLAUDE.md` via `@path` — the project's `CLAUDE.md` holds only
project-specific bindings (name, description, build/test commands, paths, language rules)
plus the import line. Everything here is meant to apply unchanged across projects and
languages; if you find yourself editing this file to fix something project-specific,
that content belongs in the local `CLAUDE.md` instead.

---

# General Rules

- Ask, don't assume.
- Self-documenting code, minimal comments.
- Minimal explanations unless requested.
- No preambles or summaries unless explicitly needed.
- Avoid redundant comments that are obvious mid-method.
- Emphasize clean module boundaries.
- Favor explicit interfaces (headers, type declarations, public API surface — whichever
  form the language uses) over implicit coupling.
- Avoid hidden state.
- Follow the language/ecosystem's own best practices and idioms.
- IDE- or tool-generated doc-comment blocks (Doxygen, docstrings, JSDoc, etc.) per
  class/type/function are acceptable; don't flag them as style violations on sight.
  Fix inaccurate content rather than proposing removal.
- **Learning-first:** The primary goal is user understanding, not delivery speed.
  Explain the concept or trade-off before writing any code. Let the user attempt the
  implementation first; offer corrections rather than replacements.

---

# Workflow

## Role per Phase

| Phase | Who leads | AI role |
|-------|-----------|---------|
| Design & Architecture | Collaborative | Propose interfaces; discuss trade-offs; never decide unilaterally |
| Interfaces & Type Declarations | Collaborative | Discuss in prose; write only after explicit permission |
| Implementation (source files) | **User** | Assist only when explicitly asked; one logical unit at a time |
| Tests | **User** | Generate stubs/templates on request; full suites only on an explicit request |
| Documentation (`docs/`) | **AI** | Full control; apply directly |

## Implementation Phase Rules

These apply to any implementation/source file:

- **When given an implementation plan:** do NOT auto-implement it. Ask which part the user wants help with first. A plan is a specification to work from, not permission to execute autonomously.
- **Default scope is one logical unit** (one function, one case block, one group): when asked to write implementation code, write one unit, then stop and wait.
- **A direct request that names a larger scope is permission for exactly that scope** — "write the whole file", "implement all of it", "write all the tests for X". Do it; confirm the scope first only if it's ambiguous. Nothing beyond the named scope.
- Propose the approach in text before writing any code.
- **Prose over code in discussion:** design talk, reviews, and corrections are carried in prose —
  hints, questions, descriptions. Code appears only when the user asks for it: a snippet on request,
  or an explicit instruction to write the code.
- **Hard stop:** If you find yourself about to write more than ~5 lines of code unprompted, stop. Post a description instead. You are violating scope.

## Test Phase Rules

- Generate boilerplate or stubs for repetitive patterns when explicitly asked.
- **Never generate a complete test file unprompted**, even when tests are part of an agreed plan.
- When helping with tests: propose the fixture and one or two example cases; let the user write the rest.
- The exception: when the user explicitly says "write all the tests for X", do so — but confirm scope first.

## Code Modification Rules

For **source, interface/type-declaration, and test files** (new or existing):
- **Before permission — dialog only, in prose:** responsibilities, behavior, trade-offs. Signatures
  are code too: no declarations, signatures, or snippets unless those specific names/parameters were
  already discussed or the user asks for them.
- **After explicit permission — write what was asked for,** at the scope the request named (one unit
  by default — see Implementation Phase Rules). Show the proposal first only when a review step is
  genuinely useful; don't paste content in chat and then write the same content again.
- For modifications: one focused edit per logical change — not a whole-file rewrite.
- Never output a full file as a replacement unless the user explicitly asks for it.
- **Interface/type-declaration files follow the same rules as implementation files** (if the language separates them into their own files at all — e.g. C/C++ headers). A header is not an exemption for "just proposing" code.

For **documentation files** (`docs/`):
- AI has full control. Apply edits directly.
- Includes: `index.md`, `FILES_INDEX.md`, `ARCHITECTURE.md`, module-level docs, `patch_notes/`.
- `TASK.md` is a **workflow definition** — edited only when the session workflow itself changes. Not a task descriptor.
- `docs/PROGRESS.md` is **append-only** — one entry per completed task. Never rewritten wholesale.
- `docs/patch_notes/` — AI creates a consolidated note **only when the user asks**. See patch_notes rules below.
- `FILES_INDEX.md` — AI updates automatically whenever files are added, removed, moved, or renamed.

Never assume documentation filenames.

---

# Documentation Rules

- Documentation directory is `docs/`.
- Module-level docs live in `docs/modules/<Module>.md`.
- New module docs created only when code for that module exists.

## Task Completion Doc Audit

Run it whenever staging a commit at task/session end, or when the user explicitly asks to update
docs. The checklist lives in `TASK.md` § Task Completion (step 1.5) — kept there only, so the two
files can't drift apart. "Prepare a commit" means audit + stage + summary, never the commit itself —
see `TASK.md` § Commit Phrasing.

## Documentation Lifecycle
- Docs are living files updated continuously by AI.
- AI maintains cross-links between interface files, implementation files, and documentation.
- AI maintains `index.md` to reflect the overall project structure and module layout.

### Internal Document Structure
Prefer flat `##`-per-concern sections over nesting unrelated concerns as numbered subsections
under one umbrella heading — distinct from the Splitting Large Docs rule below (which is about
breaking one doc into several files, not a single doc's internal layout). Full rules for how
generated documents (`CLAUDE.md`, PRD, etc.) get assembled and formatted, including this one,
live in `templates/DOCUMENTATION_STYLE.md` — kept there rather than here since it's only
consulted during doc generation, not every session.

### Archiving
- When a doc section becomes obsolete (describes removed code, superseded design), AI moves it to `docs/archive/<filename>_<date>.md`.
- Archive entries are removed from `index.md` but listed in `docs/archive/INDEX.md`.
- AI creates `docs/archive/` directory on first archive operation.

### Splitting Large Docs

- If any doc exceeds ~300 lines, treat that as a trigger to **check whether it should split, not an order to split it.** The line count means "go look for a seam," not "this file must shrink."
- **Before splitting, decide whether a real seam exists:**
  1. Look for a real grouping-construct boundary first — whatever the language calls it: namespace, module, package, class, or (for a language with none of those) simply the file. A doc that *looks* monolithic often turns out to be multiple concerns glued together — untangling it can surface real staleness/duplication bugs, not just length.
  2. If such a seam exists, split along it per the naming convention below.
  3. If there's no grouping-construct seam but the doc has independently-queried facets (e.g. "public contract" vs. "implementation notes" vs. "test coverage" for one unit) — split by *concern* using a `Group` doc, not by arbitrary line ranges.
  4. If the doc is genuinely one cohesive narrative — no real seam, no independently-queried facets — do **not** force a split just to hit the line target. Keep it one file and improve internal navigation instead (consistent `##` headers, a short TOC at the top) so a single fact is still cheap to find via grep or section-jump. Tell the user this was the call and why, rather than silently leaving it long or silently fragmenting it.
- **Non-code files** (data, config, schemas, generated assets — JSON, SQL, YAML, etc.) are not modules and don't get their own namespace-chain treatment. They're documented under whichever folder they live in, or as a data-reference note attached to the code module that actually reads/writes them.
- Split strategy (once a seam is confirmed): extract large sections into their own files, leave a summary + link in the parent doc.
- **Naming convention — two-level separator system:**
  - **Single `_`** = a real grouping-construct separator. Each segment must correspond to an actual namespace/module/package/class in the code. `Foo_Bar_Widget.md` traces `Foo::Bar::Widget` exactly (or `foo.bar.Widget` in a language that nests packages by dot, or whatever the language's own separator looks like) — same mechanical rule regardless of syntax.
  - **Double `__`** = doc-only logical grouping at the current level. Used when the concept being documented has no matching construct in the code — it is an aggregate invented for documentation purposes. `Foo__Lifecycle.md` means "within the `Foo` grouping, grouped under the doc concept 'Lifecycle'."
  - A filename ending at a real file (not a grouping construct) is also single-`_`: `Foo_Bar_Widget_Helpers.md` maps to one specific file inside `Foo::Bar::Widget`.
  - Examples:
    - `Foo_Bar_Widget.md` — covers the `Foo::Bar::Widget` grouping (overview, links out to per-file docs)
    - `Foo_Bar_Widget_Helpers.md` — covers one file's content
    - `Foo__Lifecycle.md` — doc-level aggregate: lifecycle concerns within `Foo` (no matching construct)
  - **Sub-groupings within an aggregate** (an aggregate that itself splits further) use single `_` after the `__`, not a second `__`: `Foo_Bar__Registry_Section0.md`, not `Foo_Bar__Registry__Section0.md`. The double underscore only needs to mark the one real→invented transition point once; the `**Group:**` header line inside the doc (below) disambiguates the rest.
- **Required header line — Namespace / Module / File / Group:** the single-`_` filename convention alone can't distinguish a grouping-level doc from a file-level doc — both look identical in structure. Every module/split doc's first content line after the title **must** state which kind it is:
  - `**Namespace:** \`Foo::Bar::Widget\`` (or `**Module:**`/`**Package:**`/`**Class:**` — whichever term the language uses) — covers the grouping and (usually) links out to its per-file docs
  - `**File:** \`path/to/Helpers.ext\`` — covers one specific file
  - `**Group:** <name>` — doc-only aggregate (double-`_` filename); state what the group covers since the name isn't derived from code
- This convention applies to any module/package/namespace in the project.
- `FILES_INDEX.md` follows the same rule: when it grows too large, AI extracts per-module sections into `docs/modules/<Module>_FILES.md` and keeps the root `FILES_INDEX.md` as a summary with links.

---

# Session & Task Documents

## TASK.md — Workflow Definition
- Defines **how** the session-task workflow operates, not what the current task is.
- Updated only when the workflow itself changes (rare; treat like changing a process document).
- AI reads this at every session start to learn the rules of engagement.
- Lives alongside this file (not textually merged into it) so the on-demand-loading discipline it defines — `PROGRESS.md`, module docs, and `FILES_INDEX.md` are never auto-loaded — isn't undermined by folding it into something that *is* auto-loaded. See Session Loading Strategy below.

## Task Working Directory — Session Context
- User-provided folder outside the repo; not committed.
- AI creates and maintains files freely inside it: context notes, findings, analyses, diffs, todos, caches, logs, etc.
- **Task tracker file:** AI creates `<task-name>.md` inside this dir. This is the idempotent session state:
  - Task name, goal, status, completion criteria
  - Running decisions, todos, progress notes
  - Links to relevant `docs/` entries
  - Updated throughout the session; a new session resumes by locating it; AI creates it if absent.

## docs/PROGRESS.md — Milestone Tracker
- Tracks progress toward the project's own requirements/roadmap doc.
- **Append-only** entries per completed task: `## [YYYY-MM-DD] <task-name>`
- Multiple devs append non-conflicting entries — no merge conflicts during active work.
- Links to `patch_notes/` for detail.

## docs/patch_notes/ — Consolidated Change Notes
- AI creates a note here **only when the user explicitly asks**.
- A patch note covers one **logical task** — which may span many commits and sessions.
- When asked: run `git log -- docs/patch_notes/` to find the last commit that added a patch note;
  that commit is the boundary. Generate a consolidated note covering all commits since then.
- The patch note is committed alongside any other task-completion changes; the commit that adds
  the file IS the boundary marker for the next patch note — no special message convention needed.
- Naming: `YYYY-MM-DD_<task-name>.md`
- Content: commits covered, what changed, key decisions, files touched.
- **Active cap: 4 files.** Before creating a new one, if the cap is exceeded: **move** (do not recreate) the oldest file into `docs/patch_notes/archive/` using a version-control move, then update `docs/patch_notes/archive/INDEX.md`.

- The **permanent product requirements** are maintained in a dedicated PRD-equivalent file and are **never overwritten**.

## Project Memory — Kit-Seeded vs. Project-Grown

- `workflow-init` seeds the project's auto-memory from `templates/memory/` in the shared repo. A
  memory whose frontmatter has `metadata.kitTemplate` is **kit-sourced**; anything else is
  **project-grown**. Seeded memories reinforce rules in this file — they don't replace them.
- **A project memory wins** where it differs from this file or a template, the same way the
  project's own `CLAUDE.md` does. A difference can be a deliberate project override — full or
  partial — so never "fix" a memory back to the kit's wording on your own.
- **Report, don't repair:** when a kit-sourced memory's `kitVersion` is lower than its template's
  current `kitVersion` (checked during every task-completion doc audit — see `TASK.md`), tell the
  user once that `/workflow-update` is due. It
  three-way-merges the memory, keeping the project's overrides and resolving conflicts with the
  user.
- Never edit the `kitTemplate` / `kitCommit` / `kitVersion` keys by hand — they are the merge base.

## context_dir/ — AI Scratch & Ideas (Uncommitted)

- Lives at the repo root, gitignored except `.gitkeep`. Distinct from both the **task working
  directory** (user-provided, external to the repo, per-task) and `docs/` (committed,
  AI-maintained project documentation).
- AI's own playground for ideas, future decisions, or notes that aren't ready to commit and
  don't fit the `docs/` tree — e.g. speculative architecture ideas unrelated to current scope.
- AI may create and organize files here freely, without asking each time.

---

# FILES_INDEX.md

- AI automatically maintains a file map for all interface files, implementation files, and tests.
- The file map contains:
  - File path
  - Module or subsystem
  - Main classes/functions
  - Purpose / description
- AI uses this map to plan actions, refactoring, and documentation without reading every file repeatedly.
- AI updates `FILES_INDEX.md` whenever files are added, removed, moved, or renamed.

---

# Code Analysis

- Always read related interface files before proposing changes.
- Propose improvements before rewriting code.
- Maintain modular boundaries and clear dependencies.

---

# Refactoring & Updates

- AI may propose refactoring (e.g., renaming functions, moving files, restructuring modules).
- For source/interface/test files: propose in prose first; edit only after explicit permission (see Code Modification Rules).
- For documentation: AI updates directly.
- All updates are reflected in `FILES_INDEX.md` and documentation automatically.

---

# Auto-Documentation Indexing System

- Always maintain a central `index.md`.
- Whenever a new doc is created, renamed, or moved, update `index.md`.
- Module docs in `docs/modules/` (one file per module).
- Ensure cross-links between related interface files, implementation files, and documentation.
- `ARCHITECTURE.md` (or project-equivalent) remains high-level summary and starting point.
- Index system reflects the project structure dynamically.

---

# Session Loading Strategy

Authoritative sequence lives in `TASK.md` § Session Boot Sequence — kept there only,
not duplicated here, so the two documents cannot drift out of sync, and so it stays a
file the agent *reads on cue* rather than content force-loaded into every session
regardless of need. `TASK.md` lives alongside this file, in the same shared workflow
repo — not under the connected project's own `docs/`. The project's local `CLAUDE.md`
holds the path to this repo (its `$setupRepo`-style variable); resolve `TASK.md`
relative to that same path. `TASK.md` also holds Session Context Limit handling,
Resuming an Interrupted Session, Commit Phrasing, Task Completion, and Multi-Developer
Notes.

---

# Response Protocol (Token Optimization)
1. **Plan:** What I'll do (bullets)
2. **Result:** ✓ Modified [files]
3. **Notes:** Only if deviated
4. **Bug:** Full explanation

---

# Error Handling
- Try alternatives automatically.
- If 2+ repeats fail, stop and report.
