---
title: SCA extension — current state
date: 2026-11-16
tags: [project, sca, vscode, status]
status: active
source: [[source-sca-repo-2026-11-14]]
related: [[overview]], [[implementation-plan]], [[0003-vscode-extension-and-test-strategy]], [[0005-own-engine-issue-monitor-and-triggers]], [[car-control-test-field]]
---

# SCA extension — current state

## Summary
As of `sca` commit `5b41f2c` (2026-11-16, plan block 8, pushed), SCA is a working analyzer.
It has its own engine, run-time, memory and function checks, about 40 coding rules, HIS metrics, reports, a CLI and CI.
On car-control it finds **68 of 76** expected issues in the 50 covered categories, with **0 false positives**.
10 categories (11 issues: strings and formats, floating point, concurrency) have no check yet.

## Details
**Editor (VS Code)**
- Issue Monitor view (own "SCA" container, shield icon): file → findings, severity icons, certainty in the text. Diagnostics go to squiggles and the Problems panel.
- Add / Remove / Remove All from Monitor (Explorer, editor, tab, view, palette). Folders expand to their C/C++ files. The set is stored per workspace.
- Triggers: on save (incl. autosave) with a 300 ms debounce, on Add to Monitor, and on `SCA: Analyze Now`. A newer run cancels the older one. Status bar shows "analyzing… / N issues".
- Rule help: hover shows a summary, a fix and an Explain link. `SCA: Explain Rule`, `SCA: Show Control-Flow Graph`, `SCA: Check Backends`, `SCA: Export Report`, Save/Clear Baseline.

**Core (`src/core/`, no VS Code imports)**
- Parser: web-tree-sitter + tree-sitter-c / -cpp (WASM). The tree is built before preprocessing.
- Project: `compile_commands.json` (GCC/Clang/MSVC flags), a small preprocessor (`#if` evaluation, macros, includes, guards), `.sca.json` with a JSON schema, `sca-justify(...)` comments and deviations, a baseline file.
- Value engine: CFG per C function, value ranges with zero witness, init state, pointer null state, a fixed-point solver with widening, narrowing on conditions. It has file summaries (static stores, same-file returns, call-site args of `static` functions) and a time budget per function and per file. Headers are read for typedefs, struct fields, enums, globals and register macros.
- Run-time checks (`runtime-bugfinder:`): div-zero, null-deref, uninit-read, out-of-bounds (incl. memcpy/memset/memmove), overflow, uint-wrap, invalid-shift, conversion, dead-code, endless-loop, stack-escape.
- Memory lifetime (`runtime-bugfinder:`, block 7): memory-leak, double-free, use-after-free, bad-free, dangling-pointer, unchecked-alloc, alloc-size, sizeof-pointer, pointer-arith. Design: [[0006-memory-blocks-in-the-value-engine]]. Sample `tests/samples/memory_checks.c`.
- Functions and values (`runtime-bugfinder:`, block 8): missing-return, invalid-enum (enum CTypes carry their constant values), no-return-call (a same-file "never returns" summary; such calls end the path for all checks), bad-function-pointer (syntactic: casts from a data object; fixed integer addresses allowed). Sample `tests/samples/function_checks.c`.
- Coding rules (`sca-own:`): about 40 rules in expressions, control flow, types and literals, and declarations, plus recursion over the call graph. A rule registry has own-words text and tested bad/good examples. Some rules are off by default (magic-number, increment-in-expression).
- `his-metrics:` cyclomatic, nesting, statements, parameters, returns, calls, comment density. Limits come from `.sca.json`.
- Optional backends: cppcheck and clang-tidy run as separate processes, and their findings are merged with SCA rules on the same line.
- The catalog (`catalog/rules.json`, `docs/rules.md`) is generated from the registry, and a test fails when it is stale.

**Headless and CI**
- `npm run benchmark -- <project>` checks against `expected_findings.csv` (found / missed / false positives / unmarked).
- `npm run analyze` writes text, CSV, SARIF 2.1.0 or HTML, with `--fail-on` and `--baseline`. `npm run perf` is the speed test.
- GitHub Actions run unit and integration tests on Linux and Windows, the car-control benchmark with `--fail-on-fp`, and the speed test. They publish the SARIF report and the `.vsix` as artifacts.
- Speed: 2,000 car-control files take about 12.7 s cold (CI limit 30 s); 5,000 sample files 32.7 s cold, 2.2 s cached.

**The 8 misses in covered categories** (car-control):
- `wheel_speed.c:28` div_zero: the value is 0 in an erased NVM calibration (from another module).
- `wheel_speed.c:12` null_deref: the pointer is NULL when the NVM block is invalid (set in `main.c`).
- `can.c:28 / :63 / :75` out_of_bounds: a DLC from the bus up to 15, offset 7 writes `data[8]`, and an unchecked ID range.
- `util.c:39` out_of_bounds: `i + 1 == n` at the table edge.
- `pid.c:26` overflow: an integral with no anti-windup. `util.c:87` overflow: long digit strings overflow s32.

**Not done yet:** checks for 10 categories (see [[implementation-plan]]), cross-file analysis, bundler (esbuild), LICENSE (packaging uses `--skip-license`), Marketplace publishing, LSP decision, C++ standards.

## Open questions
- LSP server or keep logic in the extension? Still open ([[0003-vscode-extension-and-test-strategy]]).
- Concurrency (data_race, missing_unlock) is "not covered" in [[0004-rule-catalog-and-iso26262-scope]], but car-control plants it. Add a light check or keep it as a known miss?
