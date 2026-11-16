---
title: ADR-0007 Cross-file summaries, inputs and accumulators (plan block 10)
date: 2026-11-16
tags: [decision, sca, engine, cross-file, inputs, overflow]
status: accepted
supersedes:
related: [[0005-own-engine-issue-monitor-and-triggers]], [[0006-memory-blocks-in-the-value-engine]], [[implementation-plan]], [[sca-extension-current-state]], [[2026-11-16-lazy-cross-file-work-made-results-order-dependent]], [[2026-11-16-narrowing-and-the-in-bounds-assumption]]
---

# ADR-0007: Cross-file summaries, inputs and accumulators (plan block 10)

## Context
- After block 8, car-control was at 68/76 with 0 false positives. The 8 misses all needed knowledge the engine did not have: a NULL or 0 from another module (`wheel_speed.c:12`, `:28`), lengths and ids from the CAN bus (`can.c:28`, `:75`), buffer lengths the callers pass (`can.c:63`, `util.c:39`), and accumulators (`pid.c:26` integrator, `util.c:87` digit loop).
- The core policy from [[0005-own-engine-issue-monitor-and-triggers]] stays: **a finding needs evidence; unknown is not "can be anything bad"**; only bounds backed by the code or by a type count.
- Analysis on save must stay fast: another file's facts are computed once and reused.

## Options considered
1. **Whole-program analysis** (solve all files together, iterate to a global fixpoint). Most precise, far too slow for on-save use and fragile for partial projects.
2. **Per-file facts in a lazy project index**, keyed by file content, plus a few targeted "input" rules. Chosen.
3. **Treat every unknown as its type range** (pessimistic). Finds everything, but floods findings and breaks the evidence policy.

## Decision
Option 2, in four parts.

### 1. Project index (`src/core/project/projectIndex.ts`, interface `src/core/engine/knowledge.ts`)
- A text scan of every source file (no parsing) finds candidate definers (regex for definitions at column 0) and which files mention a name. Only candidates are parsed.
- **Per-file facts**, cached by content hash (one parse): exported (non-static) functions with their return summaries, explicit 0 values in struct initializers, names that comparisons or limiting assignments use.
- **Call facts** per function: all direct calls in files that mention it (each caller solved once in a new `args` mode with real return values), joined argument values, min / common array length per argument. A function used as a value (function pointer) or defined in more than one file → no facts.
- Wired through `FileContext.knowledge` / `SourceContext.knowledge` into `FileEngine`. CLI, benchmark, perf and the extension use it; the extension starts a new index generation when a C/C++ source is saved, created or deleted (`Project.sourceChanged`), and the generation is part of the cache key.
- **Time spent on other files is excluded** from the per-function / per-file budgets (`engine/clock.ts`). Results that hit a file still being computed are not cached. See [[2026-11-16-lazy-cross-file-work-made-results-order-dependent]].

### 2. What counts as evidence from other files
- Return value of a function defined once elsewhere: used, **except** a NULL / 0 that a parameter guards (`return (i < n) ? &t[i] : NULL;`). Callers usually pass valid indexes; without relating both sides that is no evidence (would have flagged `main.c:238`, `sched_task(i)` in a `i < count` loop).
- Pointer parameter of a non-static function: maybe NULL when some caller in the project passes a value that can be NULL (witness names the caller and the source).
- Array length of a pointer parameter: the smallest array the callers pass — but **not** when the function compares one of its integer parameters (`i < len`): then the caller passes the length too. Exception, the **length-parameter rule**: when every call passes exactly the length of its array for one parameter (`interp_table(xs, ys, SOC_POINTS, x)`), that parameter takes that exact value and the length is used.
- Struct field zero: an explicit 0 in a non-all-zero initializer (`erased = {0U, 1950U}`) is a zero witness for `T.f` when the object is unknown: a static object overwritten as a whole by an opaque value (`wcal = *calib`), or a field behind a non-const pointer parameter. All-zero lists (`{0}`) and assignments (`p->x = 0;` resets) are deliberately not used.

### 3. Inputs
- Volatile objects (fields declared `volatile`, volatile globals) and fields behind **pointer-to-const** parameters get the full range of their declared type as **backed** bounds — like an unchecked parameter — but **no zero witness**. Non-const pointer parameters stay unknown (state, not input: `Pid *p`).
- Fields behind pointer parameters that the function writes are tracked as pseudo variables (`->:out.dlc`), narrowed by conditions, dropped at calls, on a new pointer value, and at memcpy/memset into the object; elements (`p->data[i]`) are not tracked.

### 4. Engine narrowing and accumulators
- After the widened fixpoint, up to 2 **narrowing rounds** (only when widening happened) recompute states from the solved ones (sound). Guard: a block the fixpoint reaches stays reachable (see [[2026-11-16-narrowing-and-the-in-bounds-assumption]]).
- New check `src/core/checks/accumulate.ts` (rule `runtime-bugfinder:overflow`):
  - loop: signed `v = v * K + e` (|K| >= 2) in a loop whose condition has no `< <= > >=` (sentinel loop), and nothing in the loop compares `v`;
  - persistent: `X += step` where X lives across calls (field behind a non-const pointer parameter, or a global), the step is computed and **as wide as X's type**, X is not also decreased (`sum -= old`, sliding window), and nothing in the project compares or limits the name.

## Why
- Each new source of findings has a named reason in the message (caller + source, initializer file + line, "a loop that runs until a data value stops it"). That keeps the evidence policy and makes findings reviewable.
- Per-file content caching keeps on-save analysis cheap; the cold speed test grew from ~12.7 s to ~16.2 s for 2,000 files (limit 30 s; 14.6 s measured before narrowing was added, so roughly half each from the index and from narrowing; narrowing runs only after a loop was widened, 2 rounds).

## Consequences
- car-control: **76/76**, 0 false positives; one new unmarked finding `can.c:62` (`data[offset]`, same unchecked offset as the DEFECT on line 63 — a real defect; the answer key could get a row for it).
- Known limits / deliberately not done:
  - Facts are one level deep for returns (a function's return summary uses other files' returns, but call facts use parameters as "anything").
  - The index reads files from disk: unsaved edits in another editor are not seen until saved.
  - Type-based field zeros are an approximation: any initializer of `T` counts for every unknown `T` object.
  - Accumulator steps narrower than the target (`charge_mas += current_ma / 10`, s16 into s32) are not reported, though they overflow after very many calls; constant-step counters neither.
  - Aliasing writes to tracked `p->f` through another pointer are not seen.
