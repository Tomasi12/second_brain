---
title: Run-time error classes (C/C++)
date: 2026-11-11
tags: [area, runtime-errors, c, cpp]
status: active
related: [[static-analysis-basics]]
---

# Run-time error classes (C/C++)

Candidate checks for the analyzer. One line each; detail goes in separate notes.

| Check | Example | Notes |
|---|---|---|
| Division by zero | `x / y` with `y` possibly 0 | integer and float |
| Integer overflow | `INT_MAX + 1` | signed overflow is UB in C/C++ |
| Out-of-bounds access | `a[10]` on `int a[10]` | arrays and pointers |
| Null pointer dereference | `*p` with `p` possibly NULL | |
| Uninitialized read | `int x; return x;` | |
| Invalid shift | `1 << 32` on 32-bit int | negative or too-large shift |
| Use after free / double free | | needs heap modeling |
| Unreachable / dead code | | often a symptom of a bug |
| Non-terminating loop | | hard in general |
