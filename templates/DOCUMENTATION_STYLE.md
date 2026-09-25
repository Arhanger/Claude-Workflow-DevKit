# Documentation Style — Output Assembly Rules

Rules for *how* `workflow-init`/`workflow-update` assemble and format the documents they
generate — distinct from `templates/TEMPLATE_RULES.md`, which is about authoring a template's
question bank, not about formatting the output produced from one.

## Laws (no exceptions)

1. **Flat `##` sections per concern.** Never nest an unrelated concern as a numbered subsection
   under an umbrella heading (e.g. Roadmap or Success Criteria under "Core Subsystems" — those
   aren't subsystems). See `WORKFLOW.md`'s Internal Document Structure rule.
2. **Omit sections with no real content.** Never force a placeholder/empty section just to
   complete a checklist.
3. **Never duplicate a fact another doc already owns — link to it instead.** Apply it
   proactively during assembly rather than catching it after the fact: duplicated facts need
   updating in two places, and one of them always goes stale.
4. **Follow each artifact type's fixed section order exactly** — don't improvise a new structure
   per run. See Per-Artifact Shapes below for where each one is defined.

## Guidelines (default; deviate only with a stated reason)

- **Heading depth:** `##` for top-level sections, `###` only for genuine sub-items within one
  section (e.g. a PRD's Core Subsystems listing Foo/Bar/Baz as `###` entries). Never skip a
  level.
- **Tables vs. bullets:** a table only for genuinely tabular, multi-column, comparable-row data
  (clock speeds, memory regions). Bullets for everything else — don't force a single free-text
  list into a table just to look structured.
- **Code fences:** literal commands, paths, and config only — not prose wrapped in a fence for
  emphasis.
- **Bold:** for a leading label at the start of a bullet/line (`**Goal:** ...`), not for general
  mid-sentence emphasis.
- **Cross-references over restatement:** if the same fact would otherwise appear in two
  documents, link one to the other instead of asserting it twice.

## Per-Artifact Shapes (index, not duplicated here)

- **`CLAUDE.md`** — fixed shape (Project / Shared Workflow / Build & Test / Paths / Dependencies
  / Project-Specific Rules) defined in `workflow-init`'s `SKILL.md`, Step 4.
- **PRD** — fixed 9-section flat order defined in `templates/prd.md`'s own Output Structure
  section.
- **Module docs / `ARCHITECTURE.md` splits** — governed by `WORKFLOW.md`'s namespace-chain
  splitting convention (Documentation Rules § Splitting Large Docs) — a different concern
  (when/how to split *across files*, not how to structure *within* one), not duplicated here.
