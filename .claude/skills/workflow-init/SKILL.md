---
description: Connects a new or existing project to the shared Claude Code workflow — writes a project-local CLAUDE.md (project bindings + $setupRepo + @import of WORKFLOW.md), a PRODUCT_REQUIREMENTS.md, the rest of the doc/folder skeleton, and seeds the project's memory from the kit's memory templates, via a template-driven Q&A dialog rather than guessing.
---

# workflow-init

Scaffolds or upgrades a project to use the shared workflow. This skill's own location on disk
*is* the shared repo, reached via a symlink — Step 0 below derives its real path rather than
assuming a fixed location, so this keeps working even if the repo ever moves or this runs on a
different machine.

## Step 0 — Locate the shared workflow repo

Don't hardcode a path, and don't hardcode this skill's own name either. When this skill is
invoked, Claude Code reports its own base directory as part of the invocation context (literal
text along the lines of "Base directory for this skill: ...") — that's this skill's real
location on disk (typically a symlink into the shared repo), computed by the platform, not
something to reconstruct by name. Use *that* reported path as the input:

```bash
readlink -f "<the reported base directory>" | sed -E 's#^/([a-zA-Z])/#\U\1:/#; s#/\.claude/skills/[^/]*$##'
```

This resolves the symlink to its real target, then strips the trailing `/.claude/skills/<name>`
segment (name-agnostic — works regardless of what this skill is ever renamed to) to get the
repo root, converted to `X:/...` drive-letter form for use in `@import` lines. Use this derived
value everywhere below as `$setupRepo` — never write a literal path.

Read `$setupRepo/WORKFLOW.md` and `$setupRepo/TASK.md` now if you haven't already this
session — this skill produces files that must be consistent with the workflow they describe.

## Step 1 — New or existing project?

Check the current working directory: does `.claude/CLAUDE.md` or `docs/` already exist?

- **New project:** nothing to reconcile — proceed straight to Step 2 with a full dialog.
- **Existing project:** don't re-run a blind Q&A over content that already exists. Read the
  current `CLAUDE.md` and `docs/PRODUCT_REQUIREMENTS.md` (or equivalent) in full, then
  cross-compare them against the template question banks (Step 3): for each question, note
  whether the existing docs already answer it, answer it differently than the template would
  suggest, or don't answer it at all. Only ask about genuine gaps or contradictions — carry
  forward what's already answered correctly rather than re-asking it. State what you found
  before asking anything.

## Step 2 — Language & project-type selection

Ask which language and which project type this is. If the project already has code, look at it
first and propose the language (and build system, for Step 3) from what's there, for the user to
confirm, instead of asking cold. Look for a match under
`$setupRepo/templates/languages/` and `$setupRepo/templates/projects/` (filenames are the
selectable values, e.g. `cpp.md`, `emulator.md`).

**If no template exists for the stated language or project type:** say so plainly, then offer
to draft one together now, on the spot — a short dialog to establish that category's static
rules and question bank (see `$setupRepo/templates/TEMPLATE_RULES.md` for the shape a template should
have), write it into the appropriate `templates/` folder, and then use it immediately for this
project. This is how the template library is meant to grow — don't silently proceed without a
template and don't refuse to proceed either.

## Step 3 — Run the dialogs

Read `$setupRepo/templates/TEMPLATE_RULES.md` first (shared design principles — question-bank
shape, using web research mid-dialog). Then, in order:

1. **Language template** (`$setupRepo/templates/languages/<lang>.md`) — static rules apply
   automatically once the language is selected; ask its questions. Use web research where the
   template rules note it helps (toolchain/convention questions), not just where the
   user already knows the answer.
2. **Project-type template** (`$setupRepo/templates/projects/<type>.md`) — same treatment. Use
   web research especially for domain-specific discovery questions (e.g. "does a conformance
   test suite already exist for this target").
3. **Build & Test** — never templated (see `templates/languages/cpp.md`'s own note on this).
   Actively scan the project directory for build-system signatures (`CMakeLists.txt`,
   `package.json`, `Cargo.toml`, `pyproject.toml`, build scripts, etc.) and propose candidate
   commands for the user to confirm or correct, rather than asking a cold blank question.
4. **PRD dialog** (`$setupRepo/templates/prd.md`) — Step 0 (existing material) first, always.
   Don't re-ask anything the language or project-type dialogs already answered (e.g. target
   hardware, reference material) — carry those answers forward into the PRD instead.

Questions that a template's `## Memories` section uses as a condition are asked here like any
other question in that template; the answers are used again in Step 8.

## Step 4 — Assemble `CLAUDE.md`

See `$setupRepo/templates/DOCUMENTATION_STYLE.md` for the general assembly laws this follows
(flat sections, omit what's empty, no duplication). This is `CLAUDE.md`'s own fixed shape:

```
## Project: <name>
<one-line persona, from the project intro/PRD elevator pitch>

## Shared Workflow
$setupRepo = <the path derived in Step 0>
Set by the `workflow-init` skill when it connects a project to the shared workflow. `TASK.md`,
referenced throughout the imported workflow below, lives alongside `WORKFLOW.md` in this same
repo, not under `docs/`.

@<the same path>/WORKFLOW.md

## Build & Test
<confirmed commands from Step 3>

## Paths
<discovered/confirmed path variables, as `$var = path` lines>

# Dependencies
<from the language template's dependency questions, plus anything discovered during Build & Test scanning>

# Project-Specific Rules
<merged: language template's static rules (only the ones that actually apply given the
answers — e.g. the PCH rule only if a PCH is in use) + project-type template's static rule(s)
+ the project's own module/subsystem list from the project-type dialog>
```

The `$setupRepo` value and `@import` target are whatever Step 0 derived for *this* machine —
never re-ask, never hardcode a literal from this skill's own instructions or from any other
project's `CLAUDE.md`.

## Step 5 — Write the PRD

`docs/PRODUCT_REQUIREMENTS.md` (or the project's equivalent path), structured from the PRD
dialog's answers, following `templates/prd.md`'s own Output Structure section exactly (flat `##`
sections, fixed order, omit what doesn't apply).

## Step 6 — Scaffold the docs skeleton and `context_dir/`

Concrete content for each, not just empty files:

- **`docs/index.md`** — the master index (see `WORKFLOW.md`'s Documentation Rules). List every
  doc created in this step with a one-line summary: a "High-Level Documents" list, then a
  "Module Documentation" list — the latter starts empty for a new project, since `WORKFLOW.md`
  only allows module docs once code for that module exists.
- **`docs/ARCHITECTURE.md`** — for a brand-new project this is a thin skeleton, not a copy of
  the PRD's Hardware Reference. Don't duplicate hardware facts here — link to the PRD for those
  instead. Include a Module Status table seeded from the project-type dialog's subsystem/module
  list, every row "Not started."
- **`docs/PROGRESS.md`** — empty append-only tracker, header + structure only, no fabricated
  history.
- **`docs/FILES_INDEX.md`** — depends on whether the project already has code, not on Step 1's
  answer (a codebase with no workflow docs is "new" to the workflow but still has code). No code
  yet: an empty shell. Code present: this is a real task — scan the actual source tree and
  populate it; don't leave it empty when there's a whole codebase to index.
- **`docs/modules/`** — empty directory, unless the optional module-doc analysis below is approved.
- **`docs/patch_notes/`** + **`docs/patch_notes/archive/INDEX.md`** — empty, header/table shell
  only.
- **`docs/KNOWN_ISSUES.md`** — empty shell.
- **`context_dir/.gitkeep`** — see `TASK.md`'s `context_dir/` section for what this directory is
  for.

**Optional, only when the project already has code, 100% user-approved — never automatic:** offer to analyze it and draft the first module docs under
`docs/modules/`. This is a substantial task (reading real source, not just templates) — always
ask explicitly and wait for a clear yes before doing it. A "sure, go ahead" default or silent
assumption is not acceptable here.

**Record which templates this project used**, so a future `workflow-update` run knows what to
diff against without re-asking: write `.claude/workflow-meta.json`:
```json
{
  "language": "<selected language template filename, e.g. cpp>",
  "projectType": "<selected project-type template filename, e.g. emulator>",
  "lastUpdated": "<YYYY-MM-DD>"
}
```

## Step 7 — Gitignore and Claude Code settings

**`.gitignore`** — assemble from two sources, don't hand-write either part:
- Workflow-universal entries (same for every project regardless of language): `.claude/settings.local.json`, `context_dir/*` + `!context_dir/.gitkeep`, common OS cruft (`Thumbs.db`, `.DS_Store`), `.env`/`.env.*`/`!.env.*.example`.
- The selected language template's own "Gitignore Fragment" section (e.g. `templates/languages/cpp.md`'s build/IDE/compiled/CMake entries) — adjust if it notes a conditional (the C++ template's CMake block only applies when CMake was actually confirmed as the build system in Step 3).

**`.claude/settings.json`** — copy from `$setupRepo/templates/settings.json.example` as-is. This is a committed, project-shared Claude-Code tuning file (output-token/timeout env values), not something to interrogate the user over at length; the shared default is fine, mention it's adjustable. **Never add `model` or effort settings to it** — those are per-user choices (plan, budget, preference), set once via `/model` / `/effort`, which save to the user's own settings. A project-level `model` would silently override every collaborator's personal default.

**`.claude/settings.local.json`** (the real, personal, gitignored file — write it directly so the project works immediately) **and `.claude/settings.local.json.example`** (the committed template) — copy both from `$setupRepo/templates/settings.local.json.example` as-is (generic `Read`/`Edit`/`Write`/`git` allow rules, generic deny rules), only filling in `additionalDirectories` — `$setupRepo` (from Step 0) for the real file, the placeholder left as-is for the `.example`. **Don't auto-add project-specific `Bash(...)` allow entries for the confirmed Build & Test commands** — that's a deliberate, security-relevant choice about which commands run without a prompt, and it's the user's to make explicitly and later, not `workflow-init`'s to bake in automatically just because it detected a build script. Adding entries like that is something a user might do by hand afterward, but it's never something `workflow-init` should generate on its own.

**Skills/commands are not wired up per project.** Discovery happens once, globally, via
symlinks from `~/.claude/skills/<name>` and `~/.claude/commands/<name>.md` into
`$setupRepo/.claude/skills/` / `.../commands/` — already done for `workflow-init` itself and
`/start`. Don't attempt to set this up per project; it's a one-time, per-machine step, not part
of scaffolding an individual project.

## Step 8 — Seed project memory

**Find the memory folder.** Claude Code tells the session where this project's auto-memory lives
(the persistent memory directory named in the session's own instructions). Use that path. If the
session has no memory directory (auto-memory turned off), say so, skip this step, and mention it
in the report.

**Collect what to seed** from the `## Memories` tables (columns: File, Index line, Condition) of:
- `$setupRepo/templates/generic.md` — always in effect; ask its `## Questions` now, since no
  earlier dialog covers them.
- The selected language and project-type templates — their questions were already asked in
  Step 3; reuse those answers.

A `Q<n>` condition refers to question *n* of the same template. Keep only entries whose condition
is `always` or matches the answer. Memory bodies are in `$setupRepo/templates/memory/`; the index
skeleton is `$setupRepo/templates/memory/MEMORY.md`.

**Write the memories:**
- `MEMORY.md`: for a new memory folder, write `templates/memory/MEMORY.md` with the project name
  and today's date filled in. If `MEMORY.md` already exists, never rewrite it — only add the index
  lines that are missing, under the right section.
- Each selected memory body: copy it into the memory folder. Its frontmatter already carries
  `metadata.kitVersion`; add two more keys under `metadata` — `kitTemplate` (the body's path
  relative to the kit root, e.g. `templates/memory/feedback_doc_audit.md`) and `kitCommit`
  (`git -C $setupRepo rev-parse HEAD`). These three keys mark the memory as **kit-sourced** and are
  the merge base `workflow-update` uses later; memories without them are project-grown.
- If the kit has uncommitted changes to a template file being copied, warn the user: the recorded
  commit won't match what was copied, so a later merge base would be wrong.
- **A memory with the same file name or the same `name` already exists and isn't kit-sourced**
  (common when connecting an existing project): don't overwrite it. Show the difference between the project's memory and the
  template, then ask: keep the project's version but link it to the template (add `kitTemplate` /
  `kitCommit`, so future updates merge into it), replace it with the template, merge the two
  together now, or leave it unlinked.
- Add each seeded memory's index line to `MEMORY.md` — under *Feedback & Working Style* for
  `feedback` memories, under *Project Memories* for everything else.

## Safety

Never silently overwrite an existing `CLAUDE.md` or PRD. If the target already exists and
wasn't fully accounted for by Step 1's gap analysis, ask before writing — offer to back up, to
merge, or (useful for testing this skill itself) to write to an alternate filename for
comparison rather than touching the real file.

## Report back

Summarize what was created/changed and where, the same way doc-audit summaries work elsewhere
in this workflow — don't just say "done." Include which memories were seeded, which were skipped
by their condition, and how any existing memories were handled. Memories live outside the repo,
so they are never part of the commit. Committing is the user's own explicit action, same as
everywhere else in this workflow (see `TASK.md`'s Commit Phrasing) — never commit as part of
this skill, even once everything is scaffolded. Once committed, the project is ready for a
normal `/start` session.