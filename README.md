# second_brain

My dev second brain. I do not want to keep everything in memory.

A Git-tracked Markdown knowledge base used together with Claude Code.
Inspired by the PARA method (Tiago Forte) and open-source starters like
`brain-starter` and `claudesidian`, tailored for building **SCA**, a C/C++ static-analysis tool
(code in the separate repo `sca`).

## Workspace layout
```
C:\AI_Claude\              workspace (open Claude Code here)
  CLAUDE.md                workspace rules: brain = memory, sca = work
  .claude\commands\        /capture, /decide, /review
  second_brain\            ← this repo (memory)
  sca\                     code repo (work)
```

## This repo
```
CLAUDE.md        rules and context Claude reads
00_inbox/        quick captures
10_projects/     active projects (static-analyzer/)
20_areas/        ongoing knowledge areas
30_resources/    references
40_archive/      finished / dropped
decisions/       architecture decision records
lessons/         lessons learned, false positives
templates/       note templates
```

Optional: open this folder as a vault in [Obsidian](https://obsidian.md) to browse and link notes.

## How it "learns"
Claude's model is not trained. The brain gets smarter because **you and Claude keep writing knowledge here**:
decisions, rules, corrections, lessons. Every new session starts from this context.
