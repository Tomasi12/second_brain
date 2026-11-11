# second_brain

My dev second brain. I do not want to keep everything in memory.

A Git-tracked Markdown knowledge base used together with Claude Code.
Inspired by the PARA method (Tiago Forte) and open-source starters like
`brain-starter` and `claudesidian`, but tailored for building a C/C++ static-analysis tool.

## Quick start
1. Open this folder in Claude Code — it reads `CLAUDE.md` automatically.
2. (Optional) Open `second_brain/` as a vault in [Obsidian](https://obsidian.md) to browse and link notes.
3. Use the commands:
   - `/capture <idea>` → inbox note
   - `/decide <topic>` → decision record
   - `/review` → weekly cleanup and summary

## Structure
```
CLAUDE.md                 rules and context Claude reads every session
.claude/commands/         custom slash commands
second_brain/
  00_inbox/               quick captures
  10_projects/            active projects (static-analyzer/)
  20_areas/               ongoing knowledge areas
  30_resources/           references
  40_archive/             finished / dropped
  decisions/              architecture decision records
  lessons/                lessons learned, false positives
  templates/              note templates
```

## How it "learns"
Claude's model is not trained. The brain gets smarter because **you and Claude keep writing knowledge here**:
decisions, rules, corrections, lessons. Every new session starts from this context.
