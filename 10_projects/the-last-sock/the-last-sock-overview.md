---
title: The Last Sock — project overview
date: 2026-11-20
tags: [game, godot, gdscript, steam]
status: active
related: [[the-last-sock-steam-release]], [[ideas]]
---

# The Last Sock — project overview

## Summary
A funny 2D side-view platformer in **Godot 4.7 / GDScript**. Code lives in
`C:\AI_Claude\sock` → github.com/Tomasi12/sock. Every lost sock is alive; you lead a
rebellion of mismatched socks against the Dryer King. All art and sound are made by code
(no asset files, so no third-party licences to clear).

## Details
- **Why Godot, not Roblox/Unreal:** the goal is a paid **Steam** game. Roblox games cannot
  leave the Roblox platform (see [[ideas]] for the separate, dropped-for-now Roblox ideas).
  Godot is MIT-licensed (free to sell), light, and Claude can write all of it as text.
  Renderer: Compatibility, so it runs on most PCs.
- **Content (sock `d197550`, 2026-11-19):** 4 worlds, 24 stages (20 levels + 4 bosses),
  4 recruitable socks (Wool, Toe, Gym, Rubber), 6 enemies, 4 bosses (Big Fluff, Iron Baron,
  Dryer King, Count Suckula), 3 stars per stage (72 total), progress saved to `user://`.
- **Target:** ~3 hours of gameplay (user's ask, 2026-11-14). Not yet measured by a human.
- **Tests:** `run_tests.ps1` runs unit checks, a mechanics smoke test and a *campaign bot*
  that plays every normal level. The level builder rejects impossible designs
  (e.g. a wall before a Toe Sock is available).
- **History:**
  - 2026-11-15 `c1cb58e` prototype → `db71757` campaign (3 worlds) → `c6d2fc5` tests + bot, bug fixes.
  - 2026-11-19 `d197550` World 4 "The Closet of Doom" (Rubber Sock, Hanger Hawk, Count Suckula);
    level map shrunk to fit, HUD hint shows key 4.

## Open questions
- **Name:** repo says "The Last Sock", user also said "The Lost Sock". Decide before the
  Steam page — the app name is hard to change later.
- **Human playtest still missing:** how long World 1 takes, where it gets boring or hard
  (by stage number), whether the generated music is annoying.
- Remaining feature ideas from the README: secret socks / reward for all stars, pause menu
  with volume sliders and key rebinding.
- Release work: see [[the-last-sock-steam-release]].
