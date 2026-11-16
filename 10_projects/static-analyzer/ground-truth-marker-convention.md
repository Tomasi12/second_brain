---
title: Ground truth via in-source markers
date: 2026-11-14
tags: [project, sca, testing, benchmark, dummy_project]
status: draft
source: [[source-car-control-repo-2026-11-14]]
related: [[car-control-test-field]], [[implementation-plan]], [[0004-rule-catalog-and-iso26262-scope]]
---

# Ground truth via in-source markers

## Summary
Every planted issue is marked by a comment on the exact line where it happens. A script turns these comments into a CSV that serves as the answer key for any analyzer.

## Details
- **Marker form:** `/* KIND(category): short reason */`, for example `/* DEFECT(div_zero): n == 0 */`.
- **Kinds:**
  - `DEFECT` = a run-time error that an analyzer *should* report (33 in car-control).
  - `RULE` = a coding-rule violation that *should* be reported (6).
  - `SAFE` = correct code; reporting it is a **false positive** (19).
- **Extractor:** `tools/expected_findings.ps1` scans `src/` and `include/` with a regex and writes `expected_findings.csv` with the columns file, line, kind, category, description.
- **Why this works:** the truth sits next to the code, so moving code keeps it correct. Regenerate the CSV after edits instead of editing it by hand.
- **Reuse:** the same convention fits SCA's own `tests/samples/` (each rule gets a DEFECT and a SAFE case, see [[implementation-plan]]). It can also serve later as tool-qualification evidence.

## Open questions
- Should SCA's `tests/samples/` adopt exactly this marker syntax, so that one comparison script serves both?
