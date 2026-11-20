---
title: MATyHOUSE store — overview
date: 2026-11-19
tags: [project, shopify, ecommerce, print-on-demand, gelato]
status: active
related: [[matyhouse-product-ideas]], [[gelato-how-it-works]], [[gelato-plans]], [[gelato-personalization-studio]], [[gelato-costs-romania-2026-11]]
---

# MATyHOUSE store — overview

## Summary
My Shopify store for custom clothes. Print on demand: Gelato prints and ships each
order straight to the customer (production in Poland for Romanian orders). I am only the
intermediary: no stock, no shipping on my side.

## Store facts (checked 2026-11-19)
- Name: MATyHOUSE — domain www.matyhouse.com
- Shopify plan: Basic
- Currency: RON — country Romania — timezone EET
- One location ("Liliacului nr 2"), fulfils online orders
- Products: **0** — catalog is empty, starting from scratch
- Gelato plan: **Gelato+ Monthly, active** — billed **€27.99/month**, next payment
  2026-12-01; no paid Gelato orders yet (dashboard, 2026-11-19). Personalization Studio and
  product discounts are available (see [[gelato-plans]])

## Installed Shopify apps (2026-11-19)
- **Gelato: Print on Demand** — the fulfilment app actually used.
- Dropshipping apps not needed for custom clothes: DSers, CJdropshipping, Syncee, xConnector.
  Candidates to remove later (fewer apps = faster store).
- Translate & Adapt (Romanian + English store), FGO Integration (Romanian invoices),
  Messaging, Theme Access, Shopify Claude Connector App.

## How Claude works with the store
- Claude is connected through the Shopify connector (works since 2026-11-19; the first
  sign-in did not finish — had to disconnect/reconnect in Connectors).
- Split of work: designs and "Publish to Shopify" happen in **Gelato**; Claude does titles,
  descriptions (RO + EN), tags, prices, collections, discounts in **Shopify**.
- Claude creates products as **drafts**; publishing only after my OK.

## Next steps
1. Pick a niche and the first 5–10 designs ([[matyhouse-product-ideas]]).
2. Set prices from the real Gelato costs ([[gelato-costs-romania-2026-11]]).
3. Turn on the Gelato Personalizer app embed for personalized gift products
   ([[gelato-personalization-studio]]).

## Open questions
- Which Shopify theme is active, and is it supported by the Personalization Studio?
- Exact Gelato+ discount for products shipped to Romania (only "up to 33%" is published).

## Store theme — "Night Journal" (2026-11-19)
- The live theme "MATyHOUSE Scandinavian" looked like a home-decor shop. Picked direction 2 of 3: **Night Journal**.
- Look: night blue #0d1322, cream text #eee8dc, lamp yellow #f2b705 (same as the MH neck label). Fonts: Libre Baskerville (like the prints), Lato, and Space Mono for "journal times".
- Homepage: hero "What do you feel tonight?" with a big yellow feeling word that changes, plus word chips → "Pages from the night journal" (product entries with time + definition) → product grid → "Why night?" → contact.
- Built as a layer on top of the Scandinavian layer: `assets/night-journal.css`, sections `night-hero` and `night-entries`, setting group "Jurnal de noapte" (toggle). The contract is in the theme's `DESIGN.md`.
- Live theme edits could not be published through the connector ("changed_on_shopify" on settings_data.json and index.json). Rebuilt the design on a copy, theme **"MATyHOUSE Night Journal"**. The connector can only publish themes it created itself, so the owner published it in Shopify admin on 2026-11-19; it is now the live theme. The old Scandinavian theme stays as a backup.
