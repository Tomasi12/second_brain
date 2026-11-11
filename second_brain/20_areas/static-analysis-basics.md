---
title: Static analysis basics
date: 2026-11-11
tags: [area, static-analysis]
status: active
related: [[runtime-errors]], [[coding-rules]]
---

# Static analysis basics

## Summary
Static analysis finds defects by reasoning about source code without running it.

## Main families of techniques
- **Pattern / AST matching** — fast, simple, good for coding rules. Misses deep bugs.
- **Data-flow analysis** — tracks values/states along paths (e.g. uninitialized variables, null).
- **Abstract interpretation** — over-approximates all possible values to *prove* absence of errors (sound). Tools in this class color code as proven safe / proven error / unproven.
- **Symbolic execution / SMT** — explores paths with constraints; precise but can explode.

## Key trade-off
**Soundness vs. precision vs. speed.** "As you code" needs speed → usually incremental analysis + fast checks, deeper analysis in the background.

## Terms
- *False positive*: warning on correct code. Kills user trust → track them in `lessons/`.
- *False negative*: real bug not reported.
- *Sound*: never misses a bug of the analyzed class.
