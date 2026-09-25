# Memory Templates

Explanation only — nothing reads this file to configure a project. What gets seeded is declared in
the templates' `## Memories` sections (`templates/generic.md` for every project, plus the language
and project-type templates); how seeding and updating work is defined in the `workflow-init` and
`workflow-update` skills.

## Why memory templates exist

Claude Code's auto-memory is per project and per user: it lives in a folder outside the repo, and
nothing in `WORKFLOW.md` reaches it. In practice, rules that live only in imported `.md` files still
get broken; the same rules reinforced in memory get broken far less often. So `workflow-init`
seeds a new project's memory from the files in this folder, and `workflow-update` keeps them in
sync later.

A seeded memory reinforces a rule — it doesn't replace it. Each one keeps the rule short, says
**Why**, says **How to apply**, and names the `WORKFLOW.md`/`TASK.md` section it reinforces, so the
full rule still has one home.

## What's in this folder

- `MEMORY.md` — the index skeleton every project gets; `/start` reads its **Next Session Entry
  Point**.
- `feedback_*.md` — memory bodies. Which ones a project gets depends on the declaring template and
  its conditions — a memory can be seeded always, or only when a question got a particular answer
  (e.g. `templates/projects/emulator.md` seeds `feedback_hardware_spec.md` only if the AI owns spec
  lookups).

## How a project's copy stays current

Each seeded copy records which template it came from and the kit commit it was copied from. Memory
is a copy, not an import, so kit improvements don't arrive by themselves — `/workflow-update`
merges them in like a git three-way merge: kit-only changes are taken, project-only changes are
kept as deliberate overrides (full or partial), and conflicting changes are resolved with the user.
Memories a project grew on its own are never touched.
