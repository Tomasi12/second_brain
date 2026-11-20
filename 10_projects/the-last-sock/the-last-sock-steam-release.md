---
title: The Last Sock — Steam release plan and money estimate
date: 2026-11-20
tags: [game, steam, release, money]
status: draft
related: [[the-last-sock-overview]]
---

# The Last Sock — Steam release plan and money estimate

## Summary
What is still needed to sell the game on Steam (as of 2026-11-19), and a rough estimate of
what a private seller in Romania keeps. Nothing of the release work is started yet.

## Details
### Missing for Steam (checked 2026-11-15)
1. **Export:** no `export_presets.cfg`, Godot export templates not installed → no `.exe` yet.
2. **Steamworks:** partner account (individual or company), bank details, tax interview
   (W-8BEN as non-US), identity check, **$100 Steam Direct fee** per game (returned after
   $1,000 gross), then a **30-day wait** before release.
3. **Store page:** capsule images in the fixed sizes, screenshots, trailer, description,
   tags. Start early — it is the marketing (wishlists).
4. **Builds:** upload via SteamPipe. Optional: achievements (needs the GodotSteam plugin).
5. **Game polish:** pause menu, settings (volume, keys), human playtest
   (see [[the-last-sock-overview]]).

### Money estimate at $6 (estimate from 2026-11-19, not tax advice)
Order of deductions: refunds (~5%) → Steam 30% → US withholding (~10%, US–Romania treaty via
W-8BEN) → Romanian CASS 10% / CAS 25% (capped base) / income tax 10% (mostly offset by US tax).

| Copies | Gross | Kept (US buyers) | Kept (mostly EU buyers) |
|---|---|---|---|
| 100 | $600 | ~$320–360 | ~$270–300 |
| 10,000 | $60,000 | ~$27,000 (~125,000 lei) | ~$23,000–25,000 |

EU prices on Steam include VAT (RO 21%), which comes out before Steam's cut.
US sales tax is added on top of the price, so it costs nothing.

## Open questions
- Sell as private person, **PFA** or **SRL**? Ask a Romanian accountant before larger sales;
  an SRL may save a few thousand dollars at the 10,000-copy level.
- Price: $6 is a placeholder.
