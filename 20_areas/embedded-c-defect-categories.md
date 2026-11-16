---
title: Embedded C defect categories (car-control set)
date: 2026-11-14
tags: [area, runtime-errors, coding-rules, embedded, c]
status: draft
source: [[source-car-control-repo-2026-11-14]]
related: [[runtime-errors]], [[coding-rules]], [[car-control-test-field]], [[ground-truth-marker-convention]]
---

# Embedded C defect categories (car-control set)

## Summary
These are the defect and rule categories planted in car-control, a working checklist of what SCA must detect in embedded C. Counts are from `expected_findings.csv` at commit `5e1d4cb`.

## Details
**DEFECT (33), should be reported:**

| Category | Count | Category | Count |
|---|---|---|---|
| out_of_bounds | 9 | conversion | 1 |
| div_zero | 6 | uint_wrap | 1 |
| overflow | 5 | wrong_sizeof | 1 |
| null_deref | 2 | stack_escape | 1 |
| shift | 2 | dead_code | 1 |
| uninit_var | 1 | missing_break | 1 |
| non_terminating_loop | 1 | data_race | 1 |

**RULE (6), coding-rule violations:** switch_default, goto, recursion, float_equality, unchecked_return, missing_volatile.

**SAFE traps (19), must not be reported:** out_of_bounds 12, uint_wrap 3, div_zero 2, overflow 1, conversion 1. These traps are mostly bounds and arithmetic, which is where a *possible-error* analysis is most likely to give false positives.

**Typical embedded sources of bad values:** unchecked parameters, CAN bus data (for example DLC > 8), erased or corrupt NVM calibration, and ADC channel indexes. These are why the free tools miss most of the defects (see [[2026-11-11-free-tools-baseline-on-dummy-project]]).

## Open questions
- data_race (ISR vs. main loop) and non_terminating_loop are in the test set, but concurrency is "not covered" in [[0004-rule-catalog-and-iso26262-scope]]. Keep them as known misses, or plan a light check?
