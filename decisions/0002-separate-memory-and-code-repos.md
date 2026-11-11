---
title: ADR-0002 Separate memory repo (second_brain) and code repo (sca)
date: 2026-11-11
tags: [decision, meta, workspace]
status: accepted
---

# ADR-0002: Separate memory repo and code repo

## Context
The second brain should hold knowledge; the analyzer needs its own codebase. The code repo is public, the brain is private.

## Options considered
1. **One repo** for notes and code — simple, but private notes would become public with the code.
2. **Two repos side by side** in `C:\AI_Claude` — `second_brain` (private, memory) and `sca` (public, work).

## Decision
Option 2.

## Why
Keeps private reasoning and work context out of the public repo, and keeps the code history clean.

## Consequences
- A workspace `CLAUDE.md` in `C:\AI_Claude` links both repos; open Claude Code there.
- `/capture`, `/decide`, `/review` live in the workspace `.claude/commands/` (not version-controlled).
- Each repo is committed and pushed separately.
