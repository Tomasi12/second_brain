---
title: ADR-0005 Own analysis engine, Issue Monitor view, analysis on save / "Add to Monitor"
date: 2026-11-14
tags: [decision, sca, engine, parser, tree-sitter, vscode, ui]
status: accepted
supersedes:
related: [[0003-vscode-extension-and-test-strategy]], [[0004-rule-catalog-and-iso26262-scope]], [[implementation-plan]], [[2026-11-11-free-tools-baseline-on-dummy-project]], [[sca-extension-current-state]]
---

# ADR-0005: Own analysis engine, Issue Monitor view, analysis on save / "Add to Monitor"

## Context
- We pay for Polyspace as You Code (PYAC), and the licence is too expensive. The goal is that SCA (our VS Code extension) takes over the as-you-code checks.
- **SCA is designed on its own.** We do not copy PYAC's UI, workflow, texts or code. We take nothing from my employer's tool either. (This note is private; the public `sca` repo never names or compares to PYAC.)
- The free tools found only 3–6 of the 33 planted defects in car-control ([[2026-11-11-free-tools-baseline-on-dummy-project]]). The checks that matter, *possible* errors from unchecked inputs, have to be our own.
- The [[implementation-plan]] put clang-tidy and Cppcheck first (steps 8–9). That would ship a thin wrapper around tools that miss most defects.

## Options considered
1. **Wrap free backends first** (as planned). Quick first findings, but few of them; external processes, compile flags needed, slower on every save.
2. **Own engine on the tree-sitter C grammar** (MIT, WASM, runs inside the extension). Fast, incremental, no external process, no install; we write all analysis ourselves. Con: it parses source *before* the preprocessor, so heavy macros can confuse it.
3. **Own hand-written C parser.** Full control, but weeks of work before the first check; the preprocessor is the hard part.
4. **Clang JSON AST** (`clang -Xclang -ast-dump=json`). Exact and preprocessed, but needs clang and correct compile flags, is slower per save, and produces large output.

## Decision
1. **Engine:** option 2. Parsing uses `web-tree-sitter` + `tree-sitter-c` in `src/core/parser/`. Everything above the syntax tree is ours: symbol and guard tracking, value ranges, and the checks in `src/core/checks/`. The core has no VS Code imports and runs headless from Node (needed for the benchmark, CLI and CI).
2. **Findings** have two certainty levels: **definite** (shown as an error) and **possible** (a warning: a value from an unchecked input that we cannot show is safe).
3. **Issue Monitor:** our own view container "SCA" in the Activity Bar with a tree view "Issue Monitor":
   - The tree is monitored file → findings, with a severity icon and a "N definite / M possible" summary.
   - Clicking a finding opens the line.
   - Findings are also pushed to a `DiagnosticCollection` (squiggles + Problems panel).
4. **Monitored set:** analysis runs only on files in the monitor.
   - Commands "SCA: Add to Monitor" / "SCA: Remove from Monitor" are available in the Explorer menu (files and folders), the editor menu and the command palette.
   - The set is stored per user in `workspaceState`. A shared list in `.sca.json` comes later.
5. **Triggers:**
   - Every save of a monitored file runs an analysis. Autosave fires the same save event, so it works too.
   - "Add to Monitor" analyzes the newly added files at once.
   - "SCA: Analyze now" runs by hand.
   - A new run cancels the running one. A short debounce (~300 ms) absorbs autosave bursts.
   - No analysis on every keystroke for now.
6. **Backends** (clang-tidy, Cppcheck, Cppcheck MISRA) become **optional extra checks** later, behind the same `Finding` type. They are no longer the core. ADR-0004's catalog and rule IDs stay as they are.

## Why
- The value is in checks the free tools don't have. Building the engine first puts effort where the gap is.
- tree-sitter is the fastest way to a parser that is safe to run on every save inside the editor, with no install step for users. Writing our own parser would delay every check by weeks.
- An explicit monitor list keeps runs fast and the results quiet, and makes it clear to the user what is being checked.
- Running on save (not on every keystroke) is predictable and cheap. Analyzing a half-typed file only gives noise.

## Consequences
- Rework [[implementation-plan]]: the engine and the first native checks move before steps 8–9, and the backends become optional (step 8+).
- New dependencies: `web-tree-sitter` + the `tree-sitter-c` WASM file. Both must be packaged in the `.vsix`, so check `.vscodeignore` and the bundler.
- **Watch: macros.** Code that relies heavily on the preprocessor (register macros in `hw_regs.h`) may parse badly. Measure this with the car-control benchmark. If it becomes a problem, add a light macro expansion step or use Clang AST (option 4) as an opt-in "deep" mode.
- Without the preprocessor and `#include` we know little about types and values across files. The first checks stay inside one function and one file.
- The benchmark on car-control (found / missed / false positives) is the measure of progress, starting today.
- Public `sca` docs describe SCA on its own terms only, with no PYAC comparison.
