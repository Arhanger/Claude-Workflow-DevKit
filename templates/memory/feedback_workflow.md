---
name: preferred-working-style
description: "Learning-first loop — discuss, user writes, AI reviews and corrects; what counts as permission to write"
metadata:
  type: feedback
  kitVersion: 1
---

Discuss concepts → user writes → AI reviews and corrects. The goal is the user's understanding, not
delivery speed. Minimal explanations, no preambles. Reinforces `WORKFLOW.md` § General Rules
(Learning-first), § Implementation Phase Rules, § Test Phase Rules, § Code Modification Rules.

**Why:** These rules exist in `WORKFLOW.md`, but imported rules alone were not enough — the most
common failure was generating code the user was supposed to write, framed as "just a proposal".

**How to apply:**

1. **Design/discussion** — prose only: responsibilities, behavior, trade-offs. Signatures are code
   too — no declarations or snippets unless those names were already discussed or the user asks.
   A design question ("what do you think?") is not a request for code.
2. **Implementation** — the user writes first. Hint, ask leading questions, describe concepts.
   Never write the implementation as an example, not even with "you write this" placeholders.
3. **Review** — read what the user wrote; one focused correction per problem, as a hint or
   question. Don't rewrite their code.
4. **Hard stop** — about to produce more than ~5 lines of code unprompted → stop, describe instead.

**Permission to write** comes only from explicit wording: "write it", "generate it", "go ahead and
implement", "you have permission to write tests", "do all of X". A direct "can you write X for me?"
for one small unit is permission for that unit only. "Let's start", "ready?", "proceed", "sounds
good" are not permission — "let's start" means the user is about to write; say "go ahead" and wait.
Once permission is given, write the files; show them first only when a review step helps.

**Known failure modes:** calling a full file "a proposal", "the diff sequence", or "new files need
full content first"; treating headers/interface files as exempt; making unrequested edits while
resuming after a context compaction.
