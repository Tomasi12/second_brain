---
title: "Source: car-control repo (dummy_project, snapshot 2026-11-14)"
date: 2026-11-14
tags: [resource, source, dummy_project, car-control, c]
status: draft
related: [[overview]], [[source-sca-repo-2026-11-14]], [[2026-11-11-free-tools-baseline-on-dummy-project]]
---

# Source: car-control repo (dummy_project, snapshot 2026-11-14)

## Summary
The test field for SCA: an embedded-style C99 ECU that contains planted bugs, each marked in the source.

## Source
- Path: `C:\AI_Claude\dummy_project`
- Repo: github.com/Tomasi12/car-control
- Commit ingested: `5e1d4cb` (latest; no uncommitted changes)
- Ingested: 2026-11-14

## What it says (summary)
- It is a small electric-drive ECU that also runs on the PC through a plant model. See [[car-control-test-field]].
- Planted issues carry `DEFECT` / `RULE` / `SAFE` markers, and these are collected into `expected_findings.csv`. See [[ground-truth-marker-convention]].
- There are 33 defects in 14 categories and 6 rule violations. See [[embedded-c-defect-categories]].
- Free tools found 3–6 of the 33 defects, with 0 false positives. See [[2026-11-11-free-tools-baseline-on-dummy-project]].
- The toolchain (GCC 14.2, Clang 19.1.1, clang-tidy, cppcheck, CMake, Ninja) lives in `third_party/mingw64`. It is about 1.5 GB and not in Git.
