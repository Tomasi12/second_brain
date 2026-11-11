---
title: Static analyzer — project overview
date: 2026-11-11
tags: [project, static-analysis, c, cpp]
status: active
related: [[static-analysis-basics]], [[runtime-errors]], [[coding-rules]]
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

## Open questions (to turn into decisions)
- Language / parser front end? (e.g. Clang/LibTooling, tree-sitter, own parser)
- Analysis depth: pattern checks only, or abstract interpretation for proofs?
- Integration: IDE plugin (VS Code / LSP), CLI, CI?
- Output format: SARIF?
- Implementation language for the tool itself?

## Status
2026-11-11 — Second brain created. No code yet.
