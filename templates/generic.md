# Generic Template

Applies to every project, whatever its language or project type. Same shape as the language and
project-type templates (see `templates/TEMPLATE_RULES.md`), used by `workflow-init` and `workflow-update`
alongside them — it has no selection step, it's always in effect.

## Questions

1. **Should the AI stay out of model/effort tuning?** — no suggestions to switch `/model` or
   `/effort` mid-session; those stay a one-time per-user choice. (yes/no, default yes)

## Memories

Memory bodies live in `templates/memory/`.

| File | Index line | Condition |
|------|-----------|-----------|
| `feedback_workflow.md` | `- [Preferred working style](feedback_workflow.md) — discuss → user writes → AI corrects; what counts as permission to write` | `always` |
| `feedback_prepare_vs_commit.md` | `- [Prepare commit ≠ commit](feedback_prepare_vs_commit.md) — never auto-commit on "prepare"` | `always` |
| `feedback_doc_audit.md` | `- [Doc audit lessons](feedback_doc_audit.md) — grep for stale status lines; verify docs against source` | `always` |
| `feedback_dont_abstract_early.md` | `- [Don't abstract early](feedback_dont_abstract_early.md) — need a real second call site before extracting` | `always` |
| `feedback_architecture_doc_scope.md` | `- [ARCHITECTURE.md scope](feedback_architecture_doc_scope.md) — narrative/patterns there; per-file detail → link to module doc` | `always` |
| `feedback_no_agent_tuning.md` | `- [No agent tuning](feedback_no_agent_tuning.md) — no model/effort suggestions; never pin model in project config` | `Q1 = yes` |
