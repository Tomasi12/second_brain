---
title: ADR-0003 SCA is a VS Code extension; two-level test strategy
date: 2026-11-11
tags: [decision, sca, vscode, testing]
status: accepted
related: [[0002-separate-memory-and-code-repos]]
---

# ADR-0003: SCA is a VS Code extension; two-level test strategy

## Context
SCA must check C/C++ code as you write it. We need an editor integration and a test setup from day one.

## Decision
- SCA is a **VS Code extension** written in **TypeScript** (compiled with `tsc`, no bundler yet).
- Activation: `onLanguage:c`, `onLanguage:cpp`. Placeholder command `sca.showInfo`.
- Engines: VS Code `^1.100.0`, `@types/vscode` pinned to `~1.100.0` (types must never be newer than `engines`).
- **Tests on two levels:**
  1. **Unit tests** (Mocha, plain Node) for `src/core/` — pure logic, no VS Code API. Fast, run often.
  2. **Integration tests** with `@vscode/test-cli` + `@vscode/test-electron` (Microsoft's recommended setup) — run in a real VS Code instance, workspace = `tests/samples/`, `--disable-extensions`.
- Lint with ESLint + typescript-eslint. `npm test` = compile + lint + integration.

## Why
- Keeping analyzer logic in `src/core/` without VS Code imports makes it fast to test and reusable (e.g. later as an LSP server or CLI).
- Integration tests catch manifest/activation mistakes that unit tests cannot.

## Consequences
- Every new check: logic + unit tests in `src/core/`, a sample in `tests/samples/`, integration test only for the editor wiring.
- Open: bundler (esbuild) before publishing; CI (GitHub Actions) running tests; LSP architecture; `vsce` packaging + LICENSE.

## Gotchas found while setting up
- **TypeScript 6 sets `types` to `[]` by default** → must list `"types": ["node", "mocha"]` in tsconfig, or `describe`/`it` are unknown.
- Integration test output contains noisy `[AgentHost]` logs from built-in Copilot — harmless.
