---
description: Record an architecture decision (ADR) in the second brain
argument-hint: <decision topic>
---

Help me record a decision about: $ARGUMENTS

1. Read existing ADRs in `second_brain/decisions/` to find the next number and check for related or conflicting decisions.
2. If context, options or reasoning are missing, ask me short questions first.
3. Create `second_brain/decisions/NNNN-<kebab-title>.md` from `second_brain/templates/decision.md`.
4. If it replaces an older decision, set the old one's status to `superseded` and link both.
5. Update `second_brain/10_projects/static-analyzer/overview.md` if an open question is now answered.
