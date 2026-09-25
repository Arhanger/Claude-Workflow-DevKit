---
name: feedback-no-agent-tuning
description: "Don't suggest /model or /effort switches; never pin model/effort in committed project config"
metadata:
  type: feedback
  kitVersion: 1
---

Model and effort are a one-time, per-user choice made at user level. Don't recommend `/model` or
`/effort` switches mid-session or per task type, except in very rare cases.

**Why:** The user wants to think about the project, not agent settings — avoiding that overhead is
part of why the shared workflow exists. A project-level `model` would also silently override every
collaborator's own default.

**How to apply:** Never put `model`/effort in a project's committed `.claude/settings.json`. Don't
bring up model/effort tuning unprompted.
