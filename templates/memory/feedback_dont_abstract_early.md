---
name: feedback-dont-abstract-early
description: "Extract a helper only with a real second call site; revert the extraction if that site disappears"
metadata:
  type: feedback
  kitVersion: 1
---

Wait for a genuine second call site before extracting a function/helper — an actual second caller in
the code as written now, not "this might be reused". If a planned second call site doesn't
materialize once the design settles, inline the helper again rather than leaving a single-call-site
abstraction standing.

**Why:** A helper extracted for a planned second use tends to outlive the design that motivated it —
when the design changes mid-task, the justification quietly disappears and the abstraction stays.

**How to apply:** When proposing an extraction, name the *second* concrete call site as part of the
proposal. If a later change removes that call site, treat it as a signal to revert the extraction,
not to keep it because it's already written.
