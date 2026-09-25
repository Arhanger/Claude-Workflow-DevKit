# Template Rules

Shared rules for every question-bank template — `generic.md`, `languages/`, `projects/`, and
`prd.md` — so they don't need repeating in each file. The memory bodies in `memory/` aren't
question banks (they're copied into a project as written); their rules are in `CONTRIBUTING.md`
§ Memory templates. This file is about *authoring* a template's question bank — for
the separate concern of how `workflow-init`/`workflow-update` format and assemble the documents
they generate *from* a template, see `DOCUMENTATION_STYLE.md` in this same folder.

## Shape

A template is a **question bank**, not prose to merge verbatim. Static rules only belong here
when they're genuinely non-negotiable once the category is selected (e.g. "C++ code must
compile to a standard" is static; "which standard" is a question). When in doubt, it's a
question, not a rule.

## Research is part of the dialog, not just Q&A

Filling a template's gaps isn't limited to what the user already knows. The skill should use
web search/fetch mid-dialog wherever it genuinely helps:

- **Tool/library discovery** — "what graphics libraries suit a 2D-only emulator" is a research
  question, not something to leave to the user's prior knowledge.
- **Specification lookups** — target-hardware timing tables, register layouts, or protocol
  details: own the lookup, don't make the user find it.
- **Conformance/reference material** — does a community test suite already exist for this
  target (the Tom Harte-style question in `projects/emulator.md`)? That's answerable by
  searching, not just asking.
- **Convention/best-practice checks** — current idiomatic tooling choices for a language/
  ecosystem combination the skill hasn't seen recently.

Bring findings back into the dialog as part of the question, not as a silent decision — e.g.
"I found two maintained options for X, here's the tradeoff, which fits" rather than picking
one and moving on. The goal is a more informed answer, not a faster one.

## Memories

A template may also declare memories for `workflow-init` to seed into the project's memory — a
`## Memories` table with columns File, Index line, Condition (`always`, or `Q<n> = <answer>` for
one of the template's own questions). Bodies live in `templates/memory/`. `templates/generic.md`
is the template that applies to every project; see it for a complete example.
