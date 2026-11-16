---
title: "Source: sca repo (snapshot 2026-11-14)"
date: 2026-11-14
tags: [resource, source, sca, vscode]
status: draft
related: [[overview]], [[source-car-control-repo-2026-11-14]]
---

# Source: sca repo (snapshot 2026-11-14)

## Summary
The SCA code repo: a VS Code extension skeleton for C/C++ static analysis. It does no analysis yet.

## Source
- Path: `C:\AI_Claude\sca`
- Repo: github.com/Tomasi12/sca (public)
- Commit ingested: `2bc4d02` (latest; no uncommitted changes)
- Ingested: 2026-11-14

## What it says (summary)
- The extension activates on C/C++ files and offers one placeholder command. Details: [[sca-extension-current-state]].
- Logic lives in `src/core/` with no VS Code imports, and is tested on two levels (see [[0003-vscode-extension-and-test-strategy]]).
- `installer.ps1` builds `sca.vsix` and installs it into VS Code in one step.
- `catalog/` holds the rule sets, the ISO 26262 mapping and the coverage slides. It is data only; every rule is `implemented: false` (see [[0004-rule-catalog-and-iso26262-scope]]).
- Tools and versions are in `sca/docs/tech-stack.md` (Node 24, TypeScript 6, ESLint 10, Mocha 12). They are not copied here so there is only one place to keep them up to date.
