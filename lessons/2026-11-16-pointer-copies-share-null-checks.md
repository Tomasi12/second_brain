---
title: A NULL test on a pointer must also apply to its copies
date: 2026-11-16
tags: [lesson, false-positive, sca, engine, memory]
related: [[0006-memory-blocks-in-the-value-engine]]
---

# A NULL test on a pointer must also apply to its copies

**What happened:** The first run of the new memory checks on car-control reported `unchecked-alloc` on `*cur` in
`dbuf_fill_and_free()`. The code is `base = malloc(..); cur = base; if (base == NULL) return; ... *cur = ...`. The
condition narrowed only `base`. `cur` was copied before the test, so it stayed "maybe NULL".

**What I learned:** Copies of one pointer are NULL together. Once values carry the block they point to, the engine can
see that `cur` and `base` are the same allocation. Narrowing (and "it was dereferenced, so it is not NULL") now applies
to every variable that holds the same block, unless one of them only *maybe* points there.

**What I will do differently:**
- Every new value fact (null, freed, size) must also update the copies of a value.
- Add the "copy before the test" pattern to the samples of every pointer check (`sca/tests/samples/memory_checks.c` has it as `free_moved_copy()`).
