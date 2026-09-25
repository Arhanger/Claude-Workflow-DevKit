---
description: Updates an already-scaffolded project to match how the shared workflow's templates have evolved since it was last set up or updated — diffs CLAUDE.md/PRD/.gitignore against the language and project-type templates the project was built from, three-way-merges kit-sourced memories (keeping deliberate project overrides), asks only about what actually changed, and brings docs up to date without a full re-dialog. Foundation only — not yet deeply tested; refine through real use the way workflow-init was refined through its own Phase 1-3 testing.
---

# workflow-update

Companion to `workflow-init`, for projects already connected to the shared workflow.
`workflow-init` builds from nothing; this brings an existing project's docs up to date with
however the shared templates have changed since — e.g. a new rule added to
`templates/languages/cpp.md` should be easy to pull into every C++ project that used it, not
something each project has to separately notice and copy by hand.

**Status: foundation only.** First-pass design, not deeply tested. Expect to refine the actual
diff/question mechanics as this gets real use — the way `workflow-init` itself only reached its
current shape after being validated against a real project across several rounds.

## Step 0 — Locate the shared repo

Same as `workflow-init` Step 0 — derive `$setupRepo` from this skill's own reported base
directory at invocation. Never hardcode it, never hardcode this skill's own name either.

## Step 1 — Know which templates this project was built from

`workflow-init` leaves a trace of this in `.claude/workflow-meta.json` (`language`,
`projectType`, `lastUpdated`). Read it.

If it's missing (a project set up before this file existed, or set up by hand, or connected via
an older `workflow-init` that predates this step): ask which templates apply — same selection as
`workflow-init` Step 2 — then write the metadata file now, so this run and future ones don't
need to ask again.

## Step 2 — Diff project against templates

Read the project's current `CLAUDE.md`, `docs/PRODUCT_REQUIREMENTS.md`, and `.gitignore`. Read
the current state of the templates recorded in Step 1: `templates/languages/<language>.md`,
`templates/projects/<projectType>.md`, `templates/prd.md` (including its Output Structure
section), and the language template's Gitignore Fragment.

Compare and categorize:
- **Template gained something** the project's docs don't reflect — a new question, a new static
  rule, a new gitignore entry. This is the main case this skill exists for.
- **Template lost something** the project's docs still reflect — ask whether to remove it, or
  whether it was always a deliberate project-specific deviation worth keeping regardless.
- **Template changed an existing question/rule's wording or scope** — flag the specific change
  and ask how it applies here; don't silently reinterpret the project's existing answer against
  the new wording.

Report the full diff before asking anything — same principle as `workflow-init`'s
existing-project gap analysis (Step 1 there): state findings first, then ask.

## Step 2b — Diff kit-sourced memories

Use the memory folder Claude Code names for this session (if there is none, auto-memory is off —
skip this step and say so). Seeded memories and their `metadata` keys (`kitTemplate`, `kitCommit`,
`kitVersion`) are described in `workflow-init` Step 8; declarations are the `## Memories` tables of
`$setupRepo/templates/generic.md` and the project's language and project-type templates.

**Kit-sourced memories** (those with `metadata.kitTemplate`) are compared three ways — like a git
merge:
- **base** — what was seeded: `git -C $setupRepo show <kitCommit>:<kitTemplate>`
- **ours** — the project's current memory file
- **theirs** — the kit's current template

Ignore metadata keys the auto-memory system adds or maintains itself (e.g. `originSessionId`,
`modified`, `node_type`) and the `kitTemplate`/`kitCommit`/`kitVersion` keys — compare the rest of
the frontmatter and the body.

| ours vs base | theirs vs base | Meaning | Action |
|---|---|---|---|
| same | same | up to date | nothing |
| same | changed | stale copy | take theirs |
| changed | same | project override | keep ours, say nothing |
| changed | changed | both moved | merge (below) |

**Overrides can be partial.** A project may have changed one part of a memory and still want kit
updates to the rest. So when both sides moved, merge by section/paragraph: changes on only one
side are applied automatically; hunks both sides changed are **conflicts** — show base, ours and
theirs for each and resolve it with the user, one conflict at a time. Never resolve a conflict
by guessing.

If the base can't be retrieved (the commit is missing from the kit's history, e.g. after a history
rewrite, or the kit isn't a git clone), fall back to two-way: show ours vs theirs and ask.

**Also check the `## Memories` tables** (generic, language, project type) against the memory folder:
- A declared memory the project doesn't have, whose condition applies → offer to seed it the way
  `workflow-init` Step 8 does (ask its question first if the answer isn't known).
- A kit-sourced memory whose template no longer exists or no longer applies → ask whether to
  remove it, unlink it (drop the `kit*` keys and keep it as project-grown), or keep it linked.

Project-grown memories (no `kitTemplate`) are never touched.

## Step 3 — Ask, only about the diff

Don't re-run the original dialog from `workflow-init`. Only ask about what Steps 2 and 2b
actually found. Everything already correctly reflected in the project's docs stays untouched — this is
an update, not a regeneration.

## Step 4 — Apply

Update `CLAUDE.md` / PRD / `.gitignore` with targeted edits reflecting the diff, using the same
assembly conventions `workflow-init` uses (its Step 4/5/7) — not a full rewrite of files that
are mostly still correct. Update `workflow-meta.json`'s `lastUpdated`.

For each memory updated or merged in Step 2b, set `kitCommit` to the kit's current commit and
`kitVersion` to the template's current version. The next run's base is then the kit template at
that commit; whatever the project kept that differs from it (an accepted override) shows up as
"ours changed" and keeps being preserved. Add or
remove `MEMORY.md` index lines for memories seeded or removed.

## Safety

Same posture as `workflow-init`, but higher stakes — this touches a real, in-use project, not a
fresh scaffold. Never silently overwrite. Show the diff and the proposed changes before applying
anything.

## Report back

Summarize what changed and why, referencing the specific template change that triggered each
update — not just "docs updated."