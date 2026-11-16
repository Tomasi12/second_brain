---
title: ADR-0004 Rule catalog with switchable rule sets; ISO 26262 scope
date: 2026-11-11
tags: [decision, sca, rules, misra, iso26262]
status: accepted
related: [[0003-vscode-extension-and-test-strategy]], [[coding-rules]], [[runtime-errors]]
---

# ADR-0004: Rule catalog with switchable rule sets; ISO 26262 scope

## Context
Polyspace lets users check/uncheck rules and rule sets (MISRA C/C++, AUTOSAR, CERT, CWE, metrics, Bug Finder, Code Prover).
We want the same, for automotive (ISO 26262), without copying Polyspace or copyrighted rule texts.

## Decision
- SCA has a **rule catalog** (`sca/catalog/`): rule sets → rules → backend tool, coverage level, ISO 26262 topic, own description, reason.
- Users enable/disable **rule sets and single rules** in `.sca.json` / VS Code settings, and record **deviations** (reason + approver).
- SCA is the front end; checks come from **backends**: SCA native, clang-tidy, Clang Static Analyzer, Cppcheck (+ MISRA addon), optionally Frama-C Eva.
- GPL/LGPL backends run as **separate processes** only.
- Rules referenced by ID; **no official MISRA/AUTOSAR texts** in the repo.

## Target coverage (14 rule sets)
- Full (2): HIS metrics, SCA own rules.
- Partial (7): MISRA C:2012, MISRA C:2023, CERT C, CERT C++, CWE mapping, ISO/IEC TS 17961, run-time bug finding.
- Own checks needed (2): MISRA C++:2023, AUTOSAR C++14.
- Not covered (3): run-time **proof** (Code Prover-style), concurrency, JSF AV C++.
- Also not covered: ISO 26262-8 **tool qualification**.

## Why
- Free backends give good MISRA C / CERT / bug-finding coverage quickly.
- Abstract-interpretation proof = years of work; Frama-C is C only.
- MISRA C++ has no free checker → biggest own effort, do after C.
- Tool qualification is process + independent assessment, not just code.

## Consequences
- **Amended by [[0005-own-engine-issue-monitor-and-triggers]]:** the own engine comes first; backends are optional extra checks.
- Roadmap: catalog+config → first findings (Clang) → MISRA C via Cppcheck → HIS metrics → SARIF/report → C++ standards → optional proofs/concurrency/qualification.
- Every rule needs a sample in `tests/samples/` (later reused as qualification evidence).
- ISO 26262 / MISRA details in the catalog are paraphrased from memory — **verify against the official documents** before relying on them.
- SCA *supports* ISO 26262 work; it does not make code compliant by itself.
