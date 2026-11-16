---
title: Lazy cross-file work made findings depend on file order
date: 2026-11-16
tags: [lesson, sca, engine, cross-file, determinism]
status: done
related: [[0007-cross-file-summaries-and-inputs]]
---

# Lazy cross-file work made findings depend on file order

**What happened:** With the project index (plan block 10), the same finding (`can.c:28`, `wheel_speed.c:28`) was there
in one run and gone in another (CLI vs benchmark, or after an unrelated engine change). Two causes, both from doing
other files' work *inside* the analysis of the current file:

1. **Time budgets counted the nested work.** The index parses and solves another file the first time a fact is
   needed, in the middle of a function's solve. That time counted against the function's 60 ms and the file's 1.5 s,
   so the function was skipped ("time budget exceeded") — only in whichever file came first.
2. **Recursion guards leaked into caches.** A file still being computed answers "unknown" to avoid loops. A
   project-wide cache (all field zeros, call facts) filled during that moment kept the incomplete answer for good.

**Fix:** an engine clock that leaves out time spent inside index work (`engine/clock.ts`), and a counter of
"busy" hits: a result computed while some file was busy is returned but not cached.

**Lesson:** lazy, cached, recursive computations need two checks: does nested work leak into the caller's budget,
and can a guard's "unknown" end up cached? Test determinism by running the same project in two different orders
(CLI and benchmark disagreeing was the hint).
