---
title: tree-sitter reads `(T)&x` as a bitwise and
date: 2026-11-16
tags: [lesson, parser, tree-sitter, sca]
related: [[0005-own-engine-issue-monitor-and-triggers]], [[sca-extension-current-state]]
---

# tree-sitter reads `(T)&x` as a bitwise and

**What happened:** The bad-function-pointer check missed `f = (Filter)(uintptr_t)&g_count;`. tree-sitter parses
the source before the preprocessor and without knowing which names are types. Here it built
`((Filter)(uintptr_t)) & g_count`: a "call" of `(Filter)` with the argument `uintptr_t`, AND-ed with `g_count`.
`(u16)(x)` is read as a call for the same reason.

**What I learned:** Any check that looks at casts must also accept the forms tree-sitter builds when the type name is
unknown. Those are `(T)(x)` as a call and `(T)&x` / `(T)*p` / `(T)-x` as binary expressions. A name that is not a
variable or function in scope is a type name.

**What I will do differently:**
- Test new cast-sensitive checks with the address-of form `(T)&x` and with a typedef'd type.
- Print the tree first (a small `SourceParser` script) when a syntactic check misses a planted case.
