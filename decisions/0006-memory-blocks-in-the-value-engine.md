---
title: ADR-0006 Memory blocks in the value engine (heap lifetime checks)
date: 2026-11-16
tags: [decision, sca, engine, memory, heap]
status: accepted
supersedes:
related: [[0005-own-engine-issue-monitor-and-triggers]], [[implementation-plan]], [[sca-extension-current-state]], [[2026-11-16-pointer-copies-share-null-checks]]
---

# ADR-0006: Memory blocks in the value engine (heap lifetime checks)

## Context
- Plan block 7 needs leak, double free, use after free, bad free, dangling pointer and unchecked malloc. These checks are about *which memory* a pointer refers to and what happened to it, not about the pointer's numeric value.
- The engine already tracks pointers as null / not null / maybe null, with conditions that narrow them. A second, separate dataflow pass would have to copy all of that condition handling.

## Options considered
1. **Separate lifetime pass** over the same CFG. Clean separation, but it duplicates the narrowing of conditions (`if (p == NULL) return;`), which the checks depend on.
2. **Extend the existing values and state.** Each pointer value names the block it points to, and the state keeps one status per block. Narrowing, joins, loops and summaries come for free.
3. **Full points-to / alias analysis.** Exact, but too slow and too big for analysis on save.

## Decision
Option 2.
- `Value.heap` names a block: `alloc:<node>` (malloc & co.), `call:<node>:<fn>` (a same-file function that returns memory it allocated), `@g` (whatever the static pointer `g` held at function start), `scope:x` (local array `x`). `hm` marks "points elsewhere on some paths".
- `State.blocks` holds the status: alloc / freed / maybe (freed on some paths) / escaped. `State.statics` is an overlay of static pointers assigned in the function. It only provides block info, so the existing null-deref behaviour of globals does not change.
- **Escape = silence.** A block stored in an object, passed to an unknown function or returned is not this function's any more: no leak, no double free on it. A call to an unknown function also clears the static overlay and the `@g` statuses.
- At a join, blocks that nobody points to on one path are dropped from that path. This avoids "maybe freed" false positives after `free(g); g = NULL;` in one branch.
- The return summary only keeps the block when it is *fresh* (allocated and still owned at the return). The lazy pass treats every pointer result as fresh, so it gives a superset of the real findings and the rule "lazy never finds less" still holds.
- Syntactic parts (alloc-size, sizeof-pointer, bad-free on arrays and addresses, pointer arithmetic on arrays) are emitted from the interpreter too, because they need its typing and array lengths.

## Why
- The checks' precision depends on path-sensitive null tests. Reusing them was the cheapest way to get 0 false positives.
- "Escape = silence" follows the project rule *a finding needs evidence* ([[0005-own-engine-issue-monitor-and-triggers]]): ownership we cannot follow is unknown, and unknown never gives a finding.

## Consequences
- car-control: all 9 memory categories found, 0 false positives (64/72 total).
- The speed test is about 8 % slower on cold runs (30.2 s → 32.7 s for 5,000 files).
- Known limits: no realloc failure path (the old block counts as escaped), no lost-on-overwrite leaks (`p = malloc(); p = q;` is caught only at a return), cross-file ownership is unknown (plan block 10), `p + 1` drops the block (so `free(p + 1)` is not a bad-free).
