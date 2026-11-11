# CLAUDE.md — Second Brain

This folder is my **second brain**: a Git-tracked Markdown knowledge base that Claude reads at the start of every session and helps keep up to date.

## Who I am
- I build developer tools. My main project here is a **static-analysis tool that checks C/C++ code as you write it** (in the spirit of Polyspace: run-time errors, coding-rule checks such as MISRA).
- I built a similar tool before at work. **This is a fresh, independent implementation.** Do not reproduce code, documents, rule texts or designs from my employer — only general, public knowledge and my own new ideas.

## How the brain is organized (PARA + extras)
| Folder | What goes there |
|---|---|
| `second_brain/00_inbox/` | Quick captures, unsorted. Process regularly. |
| `second_brain/10_projects/` | Active work with a goal and an end (e.g. `static-analyzer/`). |
| `second_brain/20_areas/` | Ongoing knowledge: C/C++, static analysis, coding rules, run-time errors. |
| `second_brain/30_resources/` | References: public specs, papers, links, tool notes. |
| `second_brain/40_archive/` | Finished or dropped items. Never delete — archive. |
| `second_brain/decisions/` | Architecture Decision Records (ADR): what we chose and **why**. |
| `second_brain/lessons/` | Lessons learned, mistakes, false positives found. |
| `second_brain/templates/` | Templates for new notes. |

## Rules for Claude
1. **Read before answering** project questions: check `10_projects/`, `decisions/` and `lessons/` first.
2. **Write things down.** When we make a decision, create an ADR in `decisions/` (use `/decide`). When we learn something, add it to `lessons/`. Unsorted ideas → `00_inbox/` (use `/capture`).
3. **One note = one idea.** Short notes, clear titles, link related notes with `[[note-name]]`.
4. **Every note has front matter** (see `templates/note.md`): title, date, tags, status.
5. **Keep it true.** If a note is outdated, update it or move it to `40_archive/` — don't let it drift.
6. **No secrets, no employer IP** in this folder.
7. Dates in ISO format: `YYYY-MM-DD`.
8. Ask me before deleting anything.

## Commands
- `/capture <idea>` — save a quick note to the inbox.
- `/decide <topic>` — record an architecture decision.
- `/review` — process the inbox, find stale notes, summarize the week.
