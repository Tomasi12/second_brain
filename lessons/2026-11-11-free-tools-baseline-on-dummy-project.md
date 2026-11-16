---
title: Free tools find almost none of the planted defects
date: 2026-11-11
tags: [lesson, baseline, clang-tidy, cppcheck, dummy_project]
status: active
related: [[0004-rule-catalog-and-iso26262-scope]], [[runtime-errors]], [[car-control-test-field]], [[embedded-c-defect-categories]]
---

# Free tools find almost none of the planted defects

**What:** `C:\AI_Claude\dummy_project` = repo github.com/Tomasi12/car-control. Embedded-style C99 ECU
(15 modules) with markers: 33 `DEFECT`, 6 `RULE`, 19 `SAFE` (false-positive traps). Ground truth in
`expected_findings.csv` (`tools/expected_findings.ps1`). First C++ version (2026-11-11, ~10 defects) found 1/10.

**Result on the C version (2026-11-11):**
- gcc 14.2 `-Wall -Wextra`: 3/33 (fallthrough, sizeof pointer, return local address).
- clang-tidy 19.1.1 (`clang-analyzer-*`, `bugprone-*`): 3/33.
- cppcheck 2.15 (`warning,style`): 6/33.
- No tool reported any SAFE line (0 false positives).

**Why:** They look for *definite* bugs on paths they can see. Fault paths in `main.c` depend on a
command-line value, so tools don't connect them to the defect. Defects that depend on an unchecked parameter (`ticks_per_rev`, `mode`, `cfg_`)
are not reported without a caller that passes the bad value.

**Lesson for SCA:** The value over the free backends is reporting **possible** errors from unchecked
inputs (Code Prover style: "orange" = cannot prove it is safe), not only definite ones. Keep this
project as the benchmark: count found / missed / false positives per release.
