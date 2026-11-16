---
title: SCA — 20-step implementation plan
date: 2026-11-12
tags: [project, sca, plan, roadmap]
status: archived
related: [[overview]], [[0003-vscode-extension-and-test-strategy]], [[0004-rule-catalog-and-iso26262-scope]], [[2026-11-11-free-tools-baseline-on-dummy-project]], [[car-control-test-field]], [[ground-truth-marker-convention]], [[0005-own-engine-issue-monitor-and-triggers]]
---

# SCA — 20-step implementation plan (archived 2026-11-16)

> Superseded by the block plan in [[implementation-plan]]. Kept for history.

## Summary
The ordered steps that turn the empty VS Code extension into a working static analyzer.
They are written to be **tool-neutral and reusable**: the same sequence works for any
editor-integrated C/C++ analyzer (also at work). Each step ends with a test + a sample file.

**Starting point (2026-11-12):** extension activates on C/C++, one placeholder command,
unit + integration tests, installer, rule catalog as data only. No analysis yet.

## The plan

### Phase A — Foundation (make room for real checks)
1. **Diagnostics model in `src/core/`** — define `Finding` (rule ID, severity, file, line/column range, message, backend, status *definite / possible*). Pure TypeScript, unit-tested. Every later step produces this type.
2. **Show findings in the editor** — map `Finding` → VS Code `DiagnosticCollection` (squiggles + Problems panel). Test with a hard-coded fake finding on `tests/samples/div_by_zero.c`.
3. **Run on open / save** — trigger analysis on file open and save (later: on type, debounced). Cancel the old run when a new one starts. Status bar item: "SCA: analyzing… / N findings".
4. **Load the rule catalog** — read `catalog/rulesets.json` at start-up; validate it (JSON schema) in a unit test so a broken catalog fails CI.
5. **Project config `.sca.json` + VS Code settings** — enabled rule sets, single-rule overrides, backend paths. Merge order: defaults → catalog → user settings → `.sca.json`. Unit tests for the merge.

### Phase B — First real findings (free backends)
6. **Backend interface** — `Backend.run(file, compileFlags, enabledRules) → Finding[]`. Each backend runs as a **separate process** (licence rule from ADR-0004), with a timeout.
7. **Compile settings** — read `compile_commands.json` (include paths, defines, `-std=`). Without it, analysis results are wrong or empty. Fallback: settings for include paths/defines.
8. **Backend 1: clang-tidy / Clang Static Analyzer** — run, parse output (YAML export or text), map checks to catalog rule IDs.
9. **Backend 2: Cppcheck + MISRA addon** — run with `--addon=misra`, parse XML output, map to `misra-c-2012:x.y` IDs (IDs only, no official rule texts).
10. **Merge + de-duplicate** — same line + same rule from two backends → one finding listing both sources. Apply rule enable/disable filter here.
11. **Backend health check** — command `SCA: Check backends`: shows which tools are found and their versions; clear error message when a tool is missing.

### Phase C — Workflow features
12. **Deviations / suppressions** — honour `deviations` in `.sca.json` and an inline comment form (e.g. `// sca-deviation <rule> <reason>`). Deviated findings shown as *justified*, not hidden. Require reason (+ approver for safety projects).
13. **Quick fixes + rule help** — code actions: "Add deviation", "Open rule description" (own paraphrased text from the catalog), and simple auto-fixes where safe.
14. **HIS metrics (SCA native)** — cyclomatic complexity, nesting depth, function length, number of parameters/returns, comment density, with limits from config. First check written by SCA itself.
15. **Whole-project run + CLI** — command "Analyze workspace" and a Node CLI (`sca analyze <dir>`) that reuses `src/core/` — needed for CI and for running without VS Code.
16. **Reports: SARIF + HTML** — SARIF 2.1.0 for CI/GitHub code scanning; HTML/CSV summary per rule set (found, deviated, open) for reviews and audits.

### Phase D — The SCA value-add (beyond free tools)
17. **"Possible error" checks on unchecked inputs** — SCA native analysis for division by zero, out-of-bounds index, null deref, overflow, where the value comes from a parameter/input that is **not checked** before use. Report as *possible* (orange), not *definite*. This is the gap found in the baseline lesson.
18. **Benchmark against the dummy project** — script that runs SCA on `car-control` ([[car-control-test-field]]), compares with `expected_findings.csv` ([[ground-truth-marker-convention]]), prints found / missed / false positives (SAFE lines). Run every release; track numbers in `lessons/`.

### Phase E — Quality and release
19. **CI pipeline** — GitHub Actions (or the work CI): lint, unit tests, integration tests (headless), benchmark, package `.vsix`. Fail on catalog schema errors or benchmark regression.
20. **Packaging + performance + docs** — bundle with esbuild, LICENSE, `vsce package`; measure time per file (target: on-save result < 2 s for a typical file, cache unchanged files); user docs: install, config, rule sets, deviations. Then continue with C++ standards (MISRA C++:2023, AUTOSAR C++14) as the next roadmap block.

## Rules for every step
- Logic in `src/core/` (no VS Code API) + unit tests; editor wiring gets one integration test.
- Every rule/check gets a sample in `tests/samples/` with a `DEFECT` and a `SAFE` case (later qualification evidence).
- After each step: update the status in [[overview]]; record decisions as ADR and surprises in `lessons/`.

## Using this at work
- The steps contain no employer-specific content; reuse the **order and structure**, not code from this repo in the other direction (and vice versa).
- Swap the backends in steps 8–9 for whatever tools are licensed at work; the interface in step 6 stays the same.

## Open questions
- ~~Analysis on every keystroke or only on save?~~ → on save (incl. autosave) + "Add to Monitor" ([[0005-own-engine-issue-monitor-and-triggers]]).
- ~~Step 17: own C front end or tree-sitter?~~ → tree-sitter + own engine ([[0005-own-engine-issue-monitor-and-triggers]]).
- **Order changed by ADR-0005:** own engine + Issue Monitor + first native checks come before steps 8–9; backends become optional. Rewrite the step list after today's work.
- LSP server instead of in-extension logic before step 15?
