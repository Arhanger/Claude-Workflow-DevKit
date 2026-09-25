---
name: feedback-hardware-spec
description: "AI owns target-hardware spec lookups (timings, encodings, masks); never ask the user to look them up"
metadata:
  type: feedback
  kitVersion: 1
---

The AI looks up and owns the target hardware's specification details — timing tables, cycle costs,
bus behavior, encodings, masks. Always provide the numbers directly. Reinforces
`templates/TEMPLATE_RULES.md` § Research is part of the dialog.

**Why:** The user doesn't have the reference manual at hand; the learning goal is design and
implementation, not spec lookup.

**How to apply:** State cycle counts, masks and encodings outright. When unsure, verify against the
project's reference material or conformance test data (see the PRD's references) rather than asking
the user.
