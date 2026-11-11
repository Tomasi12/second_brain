---
title: ADR-0001 Build our own second brain instead of cloning a template
date: 2026-11-11
tags: [decision, meta]
status: accepted
---

# ADR-0001: Build our own second brain instead of cloning a template

## Context
We need a persistent knowledge base for working with Claude Code on a new static-analysis tool.

## Options considered
1. **Clone a public starter** (brain-starter, claudesidian, PARA templates) — ready fast, but aimed at general productivity, includes unneeded code.
2. **Build our own, borrowing their ideas** — small, focused on the project, nothing foreign to review.

## Decision
Option 2.

## Why
Small and focused is easier to keep accurate. We take the good ideas (PARA folders, `CLAUDE.md`, `/capture` and `/review` commands, Obsidian compatibility) without importing unknown code.

## Consequences
We maintain the structure ourselves. Can still borrow more ideas or plugins later.
