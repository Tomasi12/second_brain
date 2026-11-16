---
title: Narrowing plus "the index was in bounds" made a loop exit unreachable
date: 2026-11-16
tags: [lesson, sca, engine, narrowing, false-negative]
status: done
related: [[0007-cross-file-summaries-and-inputs]]
---

# Narrowing plus "the index was in bounds" made a loop exit unreachable

**What happened:** To find `util.c:39` (`ys[i + 1]` after `while (i < n - 1 && ...) i++;`) the solver got narrowing
rounds after the widened fixpoint. The benchmark then *lost* `diag.c:52` (NULL from `find_or_alloc()`). That function
loops `for (i = 0; i <= DTC_MAX; i++)` and reads `dtc_table[i]`. After an array access the engine assumes the index
was in bounds (one bad index = one finding, not one per later use). With exact loop bounds this says `i < DTC_MAX` at
the end of the body, so `i <= DTC_MAX` is always true, the loop never exits, and `return NULL;` became unreachable.
Widening had hidden the effect before.

**Fix:** narrowing may tighten a block's state, but a block the fixpoint reaches stays reachable.

**Lesson:** "assume the program went on" refinements are not sound for reachability. Any precision gain (narrowing,
better loop bounds) can turn them into dropped paths; guard reachability explicitly and keep a benchmark row that
depends on a loop exit after an out-of-bounds loop.
