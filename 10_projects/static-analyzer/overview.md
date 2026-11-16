---
title: Static analyzer — project overview
date: 2026-11-11
tags: [project, static-analysis, c, cpp]
status: active
related: [[static-analysis-basics]], [[runtime-errors]], [[coding-rules]], [[sca-extension-current-state]], [[car-control-test-field]]
---

# Static analyzer — project overview

## Goal
A tool that analyzes C/C++ code **as you write it** and reports:
- likely **run-time errors** (overflow, division by zero, null dereference, out-of-bounds, uninitialized reads, ...)
- **coding-rule** violations (MISRA-style rules, own rules)

This is a **new, independent implementation**, built differently from the one I made at work.

## What I want to do differently this time
- [ ] _fill in: what was painful in the first version?_
- [ ] _fill in: what should be faster / simpler / more accurate?_

## Repos
| Folder | Repo | Role |
|---|---|---|
| `../sca` | github.com/Tomasi12/sca (public) | The VS Code extension (TypeScript): own engine, run-time checks, coding rules, metrics, reports, CLI, CI. See [[sca-extension-current-state]]. |
| `../dummy_project` | github.com/Tomasi12/car-control | Test field "car-control": embedded-style C99 ECU (no heap, `volatile` registers, 1 ms ISR, CAN + CRC, NVM, DTCs, watchdog), runs on the PC via a plant model. Planted defects, rule violations and safe traps are marked in the source; `expected_findings.csv` is the answer key; `build.ps1` writes `compile_commands.json`; `--fault 1..15` triggers the run-time defects. Used for the baseline in [[2026-11-11-free-tools-baseline-on-dummy-project]] and for the benchmark in step 18 of [[implementation-plan]]. |

## Open questions (to turn into decisions)
- ~~Language / parser front end?~~ → tree-sitter C grammar + own engine ([[0005-own-engine-issue-monitor-and-triggers]]). Clang AST possible later as opt-in deep mode.
- ~~Analysis depth: pattern checks only, or abstract interpretation for proofs?~~ → value engine with ranges and a fixed-point solver (abstract interpretation light), single file for now. Cross-file is plan block 10.
- ~~Integration: IDE plugin (VS Code / LSP), CLI, CI?~~ → VS Code extension ([[0003-vscode-extension-and-test-strategy]]) + Node CLI + GitHub Actions. Still open: LSP server?
- ~~Output format: SARIF?~~ → SARIF 2.1.0, HTML and CSV.
- ~~Implementation language for the tool itself?~~ → TypeScript ([[0003-vscode-extension-and-test-strategy]], [[0005-own-engine-issue-monitor-and-triggers]]).

## Status
2026-11-11 — Second brain created. No code yet.
2026-11-11 — Code repo `sca` created (public, github.com/Tomasi12/sca), next to this repo. See [[0002-separate-memory-and-code-repos]].
2026-11-11 — Empty VS Code extension created in `sca` (TypeScript), unit + integration tests passing. See [[0003-vscode-extension-and-test-strategy]].
2026-11-11 — `installer.ps1` added (one-step build + install). Pushed to GitHub.
2026-11-11 — Rule catalog + ISO 26262 coverage presentation in `sca/catalog/`. See [[0004-rule-catalog-and-iso26262-scope]].
2026-11-12 — 20-step implementation plan written: [[implementation-plan]]. Next: step 1 (diagnostics model).
2026-11-14 — Test field `dummy_project` (car-control) recorded as the third workspace repo. Extension still does no analysis.
2026-11-14 — ADR-0005: own engine on tree-sitter, own Issue Monitor view, analysis on save (incl. autosave) and "Add to Monitor"; free backends become optional. See [[0005-own-engine-issue-monitor-and-triggers]]. Goal: SCA takes over the as-you-code checks we now pay PYAC for.
2026-11-14 — Point 2 done in `sca` (uncommitted): `Finding` model in `src/core/finding.ts` + 12 unit tests (15 passing, lint clean). Next: point 3, Issue Monitor view.
2026-11-14 — Point 3 done in `sca` (uncommitted): Issue Monitor view (Activity Bar "SCA"), diagnostics sync, `IssueStore`; 20 unit + 6 integration tests passing. Next: point 4, Add/Remove from Monitor.
2026-11-14 — Shield icon chosen for the SCA view ("prevent"). Point 4 done (uncommitted): Add / Remove / Remove All from Monitor (Explorer, editor, tab, view buttons, palette); folders expand to C/C++ files (`sca.monitor.exclude`, default skips third_party/build); saved in workspaceState; `Analyzer` with an empty check list. 31 unit + 14 integration tests passing. Next: point 5, run on save.
2026-11-14 — Committed in `sca`: Finding + Issue Monitor (`3e4224b`), run on save + tree-sitter (`1b9d3e7`), first checks + benchmark (`b0367a3`), walkthrough slides (`722f99c`), project setup + preprocessor + reports + CI (`13f672e`), value engine (`59e2137`), run-time checks (`3fc2fe8`). car-control 36/44.
2026-11-15 — `sca` `24682e3`: ~40 coding rules, HIS metrics, rule registry + Explain, optional backends, generated catalog. CI fixes up to `c7227c5`. car-control **55/63, 0 false positives**.
2026-11-16 — Brain caught up with the code (notes were 2 days stale). Plan rewritten as blocks: [[implementation-plan]]. Next: block 7, memory lifetime.
2026-11-16 — Block 7 done in `sca` (`3b755f1`): memory blocks in the engine ([[0006-memory-blocks-in-the-value-engine]]), 9 memory checks, sample `memory_checks.c`. car-control **64/72, 0 false positives**; 219 unit + 27 integration tests pass. Next: block 8, quick wins.
2026-11-16 — Block 8 done in `sca` (`5b41f2c`, pushed with block 7): missing-return, invalid-enum, no-return-call, bad-function-pointer; sample `function_checks.c`. car-control **68/76, 0 false positives**; 224 unit + 27 integration tests pass, speed unchanged. Lesson: tree-sitter reads `(T)&x` as a bitwise and. Next: block 9, strings and format.
