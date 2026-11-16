---
title: Float macro constants had no value in the engine
date: 2026-11-16
tags: [lesson, sca, preprocessor, engine, floating-point]
related: [[sca-extension-current-state]], [[0005-own-engine-issue-monitor-and-triggers]]
---

# Float macro constants had no value in the engine

**What happened:** The new float checks found every case in a small test file. On car-control they found none.
car-control writes `expf((temp_c - TQ_TEMP_REF_C) * TQ_TEMP_SLOPE)` with `#define TQ_TEMP_SLOPE 0.9f`. Macro values
come from the `#if` expression evaluator, and `#if` only knows integers. So the macro was "unknown", the product's
bounds became "not backed", and no finding was allowed.

**What I learned:** The evidence rule ("only backed bounds give findings") makes every missing piece of knowledge a
silent miss, not a false positive. A check that works in a test file but not on the real project usually means the
real code goes through something the test file did not: macros, headers, typedefs.

**What I will do differently:**
- Write samples the way real code is written: constants as `#define`, types as typedefs, values from headers.
- When a planted case is missed, look first at whether each part of the expression has a value (`SCA: Show Control-Flow Graph`).
