# PRD Template — Question Bank

See `templates/TEMPLATE_RULES.md` for shared design principles (question-bank shape, using web
research mid-dialog).

Kept separate from the language and project-type templates so PRD content and `CLAUDE.md`
content don't get mixed up during the merge — this produces `docs/PRODUCT_REQUIREMENTS.md`
(or the project's equivalent), a distinct deliverable from `CLAUDE.md`.

Generic — applies regardless of project type. A selected project-type template may already
answer some of these (e.g. the emulator template's target-hardware and reference-material
questions) — don't re-ask something the project-type dialog already covered; carry the answer
forward instead.

A PRD is never generated from a blank guess. If the user has existing material, use it as the
primary source and fill gaps with questions; if they don't, the questions themselves are how
the PRD gets built. Either way, a dialog is required before drafting — offering a generic PRD
skeleton with placeholder text is not an acceptable substitute for actually asking.

## Output Structure

See `templates/DOCUMENTATION_STYLE.md` for the general assembly laws this follows (flat
sections, omit what's empty, no duplication). This is the PRD-specific fixed order:

1. Project Overview (elevator pitch + Motivation)
2. Target Platform
3. Hardware Reference *(only if the project-type dialog produced hardware-specific content —
   omit entirely for project types that don't have one, don't force an empty section)*
4. References
5. Core Subsystems / Requirements
6. Non-Functional Requirements
7. Roadmap
8. Deliverables
9. Success Criteria

Omit any section with no real content rather than forcing it in as a placeholder — this is a
fixed order for the sections that *do* apply, not a mandate that all nine always appear.

## Step 0 — Existing Material First

Before asking anything else:
- Do you have existing design docs, notes, specs, whiteboard photos, or reference links for
  this project already? If so, point to them — read/summarize before asking questions that
  material would already answer.
- Is there a README or prior partial documentation in the repo already? Check before assuming
  a blank slate (mirrors the same rule `/init`-style codebase scans should already follow).

## Questions

### Problem & Vision
1. **What is this project, in one or two sentences?** (the elevator pitch — becomes the PRD's
   opening framing)
2. **Why does it exist?** (the motivating problem, gap, or personal goal it addresses — not
   every project needs a "market" justification; "I want to learn X" or "this hardware
   fascinates me" is a legitimate answer)

### Scope
3. **What's explicitly in scope for v1?**
4. **What's explicitly out of scope / deferred?** — as important as what's in scope; prevents
   silent scope creep later (this workflow's own instructions already lean hard on "don't add
   unrequested scope" — this question is where that boundary gets defined for the project
   itself, not just for how the AI behaves within it)
5. **Target platform(s)?** Don't default to "wherever it might theoretically run" — ask
   directly which platform(s) are the real target given actual time/priority constraints, and
   whether anything wider is a genuine future goal or just not explicitly ruled out. A stated
   "supports Windows/Linux/macOS" that's actually only ever been built and run on one of them
   is worse than an honest single-platform scope — it reads as a claim, not an aspiration.

### Requirements
6. **What are the must-have features/capabilities for this to be considered "working"?**
7. **Any non-functional requirements?** (performance targets, accuracy/correctness bar,
   compatibility targets — whatever "quality" means for this specific project; not every
   project has all of these. Platform support is its own question above, not part of this one.)

### Roadmap
8. **Is there a natural phased breakdown?** (milestones, in what order, and why that order —
   e.g. "core loop before polish," "one subsystem fully correct before starting the next")
9. **Any hard external deadlines or dependencies?** (usually "no" for a personal project — skip
   quickly rather than force an answer that doesn't apply)

### Deliverables
10. **What tangible artifacts does this actually produce?** Distinct from Success Criteria
    below — this is "what exists when v1 is done" (e.g. a specific binary/library, a specific
    set of validated subsystems, specific docs), not "how do I know it's good." Don't skip this
    as redundant with scope (Q3) — scope is what you're building, this is what you end up
    holding.

### Success Criteria
11. **How will you know this is done, or done enough?** (a concrete, checkable definition —
    avoid vague "when it feels right"; ask for something observable, e.g. a specific game
    booting and running, a specific benchmark passing, a specific feature set complete)
