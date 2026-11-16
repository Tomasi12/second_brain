---
title: The brain fell behind the code
date: 2026-11-16
tags: [lesson, process, second-brain, sca]
related: [[implementation-plan]], [[sca-extension-current-state]], [[overview]]
---

# The brain fell behind the code

**What happened:** On 2026-11-14 and 11-15, `sca` got 11 commits (engine, run-time checks, ~40 rules, CI). The brain
notes still said "no analysis yet, next: step 1". Asked "what is next?", the notes gave the wrong answer. Only the git
history and the benchmark gave the right one.

**What I learned:** Big blocks done in one session skip the "update the brain" step. The 20-step list also stopped
matching the work once ADR-0005 changed the order, so nobody ticked it off.

**What I will do differently:**
- Plan in **blocks** that each end in a commit and a benchmark number, and update [[sca-extension-current-state]] in the same session as the commit.
- Before answering "what's next", check `git log` in `sca` against the date of the current-state note.
