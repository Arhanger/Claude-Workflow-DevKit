---
name: feedback-prepare-vs-commit
description: "\"Prepare a commit\" means audit + stage + summary, never git commit; commit only on explicit word"
metadata:
  type: feedback
  kitVersion: 1
---

"Prepare for a commit" / "prepare a commit" means: doc audit, update stale docs, stage, show a
summary with a proposed message. It does **not** mean `git commit`. Commit only on an explicit
"commit", "commit now", "go ahead and commit" or equivalent. Reinforces `TASK.md` § Commit Phrasing.

**Why:** Running `git commit` on "prepare" bypasses the user's review of what goes into history.

**How to apply:** After a prepare request, end with staged files + proposed message, then stop.
The workflow defines no commit-message format — match the repo's existing log style.
