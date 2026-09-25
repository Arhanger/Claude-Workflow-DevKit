# Contributing

Rules for changing this repo itself — `WORKFLOW.md`, `TASK.md`, the templates, and the skills.
For how the pieces fit together, see the README's "How it works"; for template authoring
principles, see `templates/TEMPLATE_RULES.md`.

## Stay project-agnostic

This repo is published for anyone to use. Nothing in it may reference a real, specific project —
not by name, and not through examples detailed enough to identify one (real class names,
namespaces, origin stories, a particular project's test counts or quirks).

When a concrete example helps, keep the concept and make the example generic: a real namespace
becomes `App::Core::Widget`, "the way project X's conformance suite did it" becomes "if a
conformance suite exists…". Before committing, grep the repo for the names of any projects the
change was developed against.

## The repo is stateless

This is a base injected into other projects, not a place to work from. It holds rules,
templates, and predefined configuration — never session state, progress notes, or per-machine
facts. Don't run working sessions from this repo; changes are developed from a connected project
and validated there (see below). Anything that is a rule belongs in a doc, template, or skill,
not in memory.

## Naming skills and commands

A skill or command name must not shadow a Claude Code built-in. A skill once named `init`
collided with the built-in `/init`, which ran instead of it — hence `workflow-init`. Check the
built-in command list before naming anything new.

## Settings templates

Templates carry only universal defaults. Never add `model` or effort settings (per-user choices),
and never have a skill generate project-specific `Bash(...)` permission entries (a security
choice the user makes manually). See `workflow-init` Step 7.

## Memory templates

- Bump a memory body's `metadata.kitVersion` on every content change — it's how a session notices
  a project's seeded copy is stale.
- Don't rename a memory body file: a rename is a removal plus an addition, and it breaks the link
  to every project's seeded copy.
- Memory bodies carry lessons, not incident history — keep them as project-agnostic as everything
  else. A project's own incidents belong in that project's memory.
- Declare a new memory in the `## Memories` table of the template that owns it
  (`templates/generic.md` if it applies to every project).

## READMEs are for reading

No skill may depend on a README to configure a project. Anything a skill acts on — question banks,
memory declarations, formats, procedures — lives in a template file or in the skill itself; a
README only explains.

## Validating a change

Changes to templates or skills are validated in three phases before they're considered done:

1. **Compare against reality.** Cross-check a real connected project's docs against the templates
   to find gaps — sections the real docs needed that the templates don't produce, or template
   content that turned out stale or redundant.
2. **Build the change** — the template, skill step, or rule.
3. **Simulate a fresh run.** Run the skill's dialog into an isolated throwaway folder (for example
   inside the connected project's gitignored `context_dir/`), diff the output against the real
   project, fix what differs, retest, then delete the test folder.

Changes to `WORKFLOW.md`/`TASK.md` go live in every connected project immediately (they're
`@path`-imported, not copied), so read them as a rule change for all projects, not just the one
you're working in.
