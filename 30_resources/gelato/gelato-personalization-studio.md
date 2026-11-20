---
title: Gelato Personalization Studio on Shopify
date: 2026-11-19
tags: [resource, gelato, shopify, personalization]
status: draft
related: [[gelato-plans]], [[matyhouse-product-ideas]], [[matyhouse-store-overview]]
source: [[source-gelato-docs-2026-11-19]]
---

# Gelato Personalization Studio on Shopify

## Summary
Lets shoppers add their own text, images or graphics to a product on my Shopify product
page, with a live preview. Gelato makes the print file automatically. Needs Gelato+
([[gelato-plans]]) — which I have.

## Three modes (chosen per product)
1. **In-page personalization** — options on the product page, live preview; several areas at once.
2. **Editor personalization** — customer designs in an editor on the store page.
3. **After purchase** — personalization hidden on the page; the customer gets an email link
   to personalize one layer after buying.

Shoppers can also edit a personalized item from the cart.

## Shopify setup
1. Admin → Online Store → Themes → active theme → **Customize**.
2. Left panel → **App embeds** → turn on **Gelato Personalizer** → Save.
3. Choose whether to show the "Personalize" tab; optional "Hide Preview" checkbox.
4. New products get personalization automatically. **Products made before** this must be
   opened in Shopify admin and **saved again** to republish.

## Building a personalized product (in Gelato)
- **Image layer:** "Add Personalized Layer" → upload image or placeholder → turn on customization.
- **Text layer:** add text, set font/size/position → turn on personalization.
- **Graphics layer:** a collection of graphics the customer picks from; give it a clear label.
- Conditions and collections via "Add Personalized Layer +". Label every element clearly,
  preview, test, then publish.

## Theme support
- Full: Craft, **Dawn**, Debut, Editorial, Minimal, Motion, Prestige, Simple, Spark,
  Streamline, Studio, Symmetry.
- Partial (small visual glitches possible): Beyond, Brooklyn, Envy, Impulse, Parallax, Supply.
- Not supported: Loft, Warehouse, Focus.
- Other themes: manual setup — copy CSS selectors (preview location, elements to hide,
  cart preview image, cart "Edit" button) into the app embed settings.

## Troubleshooting / requirements
- "Personalize" button needs the Add-to-Cart button to have class `btn-addtocart`.
- Product description must contain `<div data-gelato-customization="1"></div>`.
- "Your cart is empty" after Add to Cart → Gelato Personalizer embed is not enabled.
- Known limit: the live preview shows **one image at a time**.

## Watch out
- Personalized goods are excluded from the EU 14-day right of withdrawal → state clearly
  that personalized items cannot be returned (except for defects).
- Customer typos go straight to print — show a clear preview and a "check your text" note.

## Open questions
- Which apparel products support personalization? Not stated in the articles read.
- Max number of personalizable areas per product — not stated.
- Available fonts — not listed.
- Launch date of the Studio — not found (Help Center articles dated 2025-10-30 and 2026-05-05).
