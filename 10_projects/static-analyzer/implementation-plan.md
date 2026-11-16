---
title: SCA — implementation plan (blocks)
date: 2026-11-16
tags: [project, sca, plan, roadmap]
status: active
related: [[overview]], [[sca-extension-current-state]], [[0005-own-engine-issue-monitor-and-triggers]], [[0004-rule-catalog-and-iso26262-scope]], [[car-control-test-field]], [[embedded-c-defect-categories]], [[implementation-plan-20-steps-2026-11-12]]
---

# SCA — implementation plan (blocks)

## Summary
The work is split into blocks. Each block is one coherent piece that ends in a commit, tests, samples and a new
car-control benchmark number. Blocks 1–11 are done (2026-11-11 … 2026-11-16). Block 12 (concurrency) is next and
needs an ADR first.
This replaces the 20-step plan from 2026-11-12 ([[implementation-plan-20-steps-2026-11-12]]); ADR-0005 changed its order.

## Done
| # | Block | `sca` commits | Covers old steps |
|---|---|---|---|
| 1 | **Skeleton**: empty extension, tests, installer, rule catalog + ISO 26262 mapping | `e9a4fda` `9fadddf` `385679a` | — |
| 2 | **Editor foundation**: `Finding` model, Issue Monitor, monitor set, run on save + debounce, tree-sitter parser | `3e4224b` `1b9d3e7` | 1, 2, 3 |
| 3 | **First checks + benchmark**: div-zero (guards), switch-default, goto, headless benchmark, walkthrough slides | `b0367a3` `722f99c` | 18 |
| 4 | **Project setup + review**: compile DB, preprocessor, `.sca.json`, justify/deviations, baseline, SARIF/HTML/CSV, CLI, CI | `13f672e` | 4, 5, 7, 12, 15, 16, 19 |
| 5 | **Value engine + run-time checks**: CFG, ranges, solver, summaries; 11 run-time checks | `59e2137` `3fc2fe8` | 17 |
| 6 | **Rules, metrics, help, backends**: ~40 coding rules, HIS metrics, rule registry + Explain, optional cppcheck/clang-tidy, generated catalog; CI fixes | `24682e3` … `c7227c5` | 6, 8, 9, 10, 11, 13, 14 |
| 7 | **Memory lifetime**: memory blocks + status in the engine; 9 checks (leak, double free, use after free, bad free, dangling, unchecked alloc, alloc size, sizeof pointer, pointer arith). Design: [[0006-memory-blocks-in-the-value-engine]] | `3b755f1` | — |
| 8 | **Functions and values**: missing-return, invalid-enum (enum types carry their values), no-return-call (same-file "never returns" summary; such calls end the path), bad-function-pointer (cast from a data object) | `5b41f2c` | — |
| 9 | **Strings and formats** (`src/core/engine/strings.ts`): string-overflow, missing-terminator, overlapping-copy, format-mismatch, uncontrolled-format | `734cd78` | — |
| 10 | **Cross-file and inputs**: project index (other files' return values, call arguments and buffer lengths, struct initializers), volatile / pointer-to-const inputs as backed ranges, narrowing rounds after widening, accumulator overflow. Design: [[0007-cross-file-summaries-and-inputs]]. Built in a separate session on branch `block10-cross-file`, merged | `6e63c0f` (merge `f17ec98`) | — |
| 11 | **Floating point**: float-overflow (exp), float-invalid (sqrt / log / acos), float-conversion; float macro constants get values; `x * x` ≥ 0 | `734cd78` | — |

Result after block 6: car-control **55 / 63**. After block 7: **64 / 72**. After block 8: **68 / 76**. After blocks 9 + 11: **77 / 85**. After block 10: **85 / 85** in covered categories, **0 false positives**; one new unmarked finding (`can.c:62`, a real defect next to the planted one). Only the 2 concurrency categories have no check. Cost: 2,000 files 12.9 s → 16.3 s cold.

## Next blocks (in this order)

### 12. Concurrency, light — 2 issues (decision needed first)
`data_race` (ISR vs. main loop without volatile/critical section), `missing_unlock` (lock not released on a path).
ADR-0004 says concurrency is not covered. Decide with an ADR before building it.

### 13. Release
esbuild bundle, LICENSE, docs pass, Marketplace publishing, performance check (< 2 s on save), and the LSP decision.

### 14. C++ standards
MISRA C++:2023 and AUTOSAR C++14 rule sets on the tree-sitter-cpp grammar. The engine is C-only today.

## Rules for every block
- Logic in `src/core/` (no VS Code API) + unit tests; editor wiring gets one integration test.
- Every check gets a sample in `tests/samples/` with `DEFECT` and `SAFE` cases.
- The car-control benchmark must keep **0 false positives** (CI `--fail-on-fp`). Record the new found/missed numbers.
- After each block: update [[sca-extension-current-state]] and [[overview]], ADR for decisions, `lessons/` for surprises.

## Using this at work
The block order and structure contain no employer content and can be reused. Code goes in neither direction.

## Open questions
- Block 10 before block 9? It is the only block that improves the categories that already count. It is the biggest block.
- LSP server before block 13?
