# Templates

Explanation only — nothing reads this file to configure a project.

This folder holds everything `/workflow-init` and `/workflow-update` build a project from:

- `generic.md` — the template that applies to every project: generic questions and the generic
  memories to seed.
- `languages/<lang>.md` — one per language: static rules, a question bank, a gitignore fragment,
  and any memories that language declares.
- `projects/<type>.md` — one per project type: a question bank and any memories that type declares.
- `prd.md` — the question bank and output structure for the product requirements document.
- `memory/` — the `MEMORY.md` skeleton and the memory bodies the templates above declare; its own
  README explains how seeded memories stay current.
- `TEMPLATE_RULES.md` — the rules every template follows (question-bank shape, research during the
  dialog, how memories are declared).
- `DOCUMENTATION_STYLE.md` — how the generated documents are formatted and assembled.
- `settings.json.example`, `settings.local.json.example` — the Claude Code settings copied into a
  project.

A template is a question bank, not a document to copy: the skills ask its questions, and the
answers become the project's own `CLAUDE.md`, PRD, and memory.
