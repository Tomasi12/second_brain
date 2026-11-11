# CLAUDE.md — Second Brain (memory)

This repo is my **second brain**: a Git-tracked Markdown knowledge base that Claude reads and keeps up to date.
It is the **memory** of the workspace. The code lives in the sibling repo `../sca`.

## Who I am
- I build developer tools. My main project is **SCA**: a static-analysis tool that checks C/C++ code **as you write it** (run-time errors, coding-rule checks such as MISRA).
- I built a similar tool before at work. **SCA is a fresh, independent implementation.** Never reproduce code, documents, rule texts or designs from my employer — only public knowledge and my own new ideas.

## How the brain is organized (PARA + extras)
| Folder | What goes there |
|---|---|
| `00_inbox/` | Quick captures, unsorted. Process regularly. |
| `10_projects/` | Active work with a goal and an end (e.g. `static-analyzer/`). |
| `20_areas/` | Ongoing knowledge: C/C++, static analysis, coding rules, run-time errors. |
| `30_resources/` | References: public specs, papers, links, tool notes. |
| `40_archive/` | Finished or dropped items. Never delete — archive. |
| `decisions/` | Architecture Decision Records (ADR): what we chose and **why**. |
| `lessons/` | Lessons learned, mistakes, false positives found. |
| `templates/` | Templates for new notes. |

## Rules for Claude
1. **Read before answering** project questions: check `10_projects/`, `decisions/` and `lessons/` first.
2. **Write things down.** Decisions → ADR in `decisions/` (`/decide`). Lessons → `lessons/`. Unsorted ideas → `00_inbox/` (`/capture`).
3. **One note = one idea.** Short notes, clear titles, link related notes with `[[note-name]]`.
4. **Every note has front matter** (see `templates/note.md`): title, date, tags, status.
5. **Keep it true.** Outdated notes: update them or move to `40_archive/`.
6. **No secrets, no employer IP** in this repo.
7. Dates in ISO format: `YYYY-MM-DD`.
8. Ask before deleting anything.
