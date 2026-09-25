---
name: architecture-doc-scope
description: "ARCHITECTURE.md keeps narrative and patterns; per-file detail that duplicates a module doc becomes a link"
metadata:
  type: feedback
  kitVersion: 1
---

`ARCHITECTURE.md` is the higher-level document: keep architectural narrative, design patterns, and
cross-cutting tables there, even with some conceptual overlap with module docs. Per-file detail that
literally duplicates a `docs/modules/*.md` table (per-file status, exact member lists, signatures,
test-harness internals) must not live in both places — trim it to a short summary + link to the
owning module doc. Reinforces `templates/DOCUMENTATION_STYLE.md`'s no-duplication rule.

**Why:** Duplicated code-state snapshots need updating in two places every time something lands, and
one of the two always goes stale.

**How to apply:** For any duplicated content in `ARCHITECTURE.md`, ask: does removing it lose an
architectural concept (pattern, ownership model, execution flow), or just a code-state snapshot a
module doc already owns? Keep the former; replace the latter with a link.
