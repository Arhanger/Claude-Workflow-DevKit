---
name: feedback-doc-audit
description: "Lessons for the task-completion doc audit — grep for stale status lines, verify docs against source"
metadata:
  type: feedback
  kitVersion: 1
---

The audit procedure itself is defined in `WORKFLOW.md` § Task Completion Doc Audit and `TASK.md`
§ Task Completion — follow those; this memory keeps only the lessons for doing it well.

**Why:** Audits done from memory of which docs "usually" change miss docs — the fix then needs extra
commits instead of travelling with the task. And a doc's existing structure is not evidence that its
content is correct.

**How to apply:**
- Don't audit from memory. Grep `docs/` for the name of what changed **and** for the previous
  "pending"/"next" list that mentioned it (e.g. `FeatureA, FeatureB pending`) — every hit is a
  status line to update.
- When checking whether a doc matches the code, read or grep the actual source for its claims
  (names, namespaces, behavior) instead of trusting how the doc is already organized.
- If placement is ambiguous (new doc vs existing, which module owns a concept) — ask the user.
