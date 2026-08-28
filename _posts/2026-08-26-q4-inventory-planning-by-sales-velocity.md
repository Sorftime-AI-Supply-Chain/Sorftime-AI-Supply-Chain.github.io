---
title: "Q4 Inventory Planning Without the Spreadsheet — Use Sales Velocity, Not Vibes"
date: 2026-08-26
type: how-to
target_platforms: [github, dev.to, linkedin]
status: 待审核
tags: [MCP, Amazon, FBA, inventory, Q4, sorftime-seller-agent]
cover_image: /assets/images/posts/amazon.png
description: "I'm a beginner seller on Amazon US with a $10K budget. Use the Hidden Profit Index to find blue ocean product opportunities in kitchen storage — low competition, under $30, at least 300 monthly sales."
---

# Q4 Inventory Planning Without the Spreadsheet — Use Sales Velocity, Not Vibes

Every August, Amazon sellers face the same question: how much do I ship to FBA for Q4? The wrong answer on either side is costly. Ship too little and you sell out by Cyber Monday, lose ranking, and watch competitors take the buy box you spent all year building. Ship too much and you pay storage fees from January through August for inventory you will not move until next November.

The decision used to be made on vibes. Last year you sold 2,400 units, so this year ship 3,000. A friend sold out in week two, so you should ship earlier. None of this is data. It is narrative dressed up as planning.

A defensible Q4 plan rests on three inputs you can pull live: your trailing 90-day sales velocity, your current BSR trajectory, and the seasonal multiplier for your category. The plan is the multiplication of those three numbers. The work is making sure all three are honest.

---

## Why Vibes-Based Planning Fails

There are three specific failure modes for spreadsheet-free planning:

- **Last year's number is the wrong anchor.** If your price changed 20% since last Q4, your unit count is the wrong anchor. Same for category shifts (you entered a new subcategory in March), or product changes (you launched a variation in July).
- **Velocity is not the same as volume.** A SKU that sells 100 units a day in October but 0 units a day in February is not "moving 50 units a day on average." Velocity is what you sell when you are selling. Volume is what you sold. The Q4 plan needs velocity.
- **Seasonal multiplier is a category-level input, not a SKU-level guess.** Beauty multiplies by 4x at peak. Home goods multiplies by 2.5x. Pet supplies barely moves. Without the category multiplier, the plan is fiction.

A useful Q4 plan models all three correctly. Done manually, it requires pulling each SKU's trailing sales velocity, looking up BSR history, and finding the right category multiplier. For a 50-SKU catalog, it is two hours of focused work.

---

## The Manual Workflow This Replaces

To build the plan by hand:

1. Open Amazon Seller Central, download 90-day sales report for each SKU.
2. Compute trailing 30-day daily average per SKU.
3. Pull BSR history for each SKU, identify whether it is climbing or flat.
4. Cross-reference your category with a published seasonal multiplier table (Jungle Scout, Helium 10, or vendor reports).
5. Multiply velocity × multiplier for each SKU.
6. Adjust for current inventory levels already in FBA.
7. Adjust for in-transit inventory.
8. Adjust for sales velocity trajectory (accelerating vs decelerating).
9. Build the shipment plan.

It is a careful, structured exercise. It is also exactly the kind of exercise where you want the data to be fresh and the math to be transparent.

---

## The MCP Approach

With the Sorftime Seller Agent, the same plan is one prompt that pulls each input live and prints the shipment recommendation per SKU:

```
Build my Q4 inventory plan for these 50 SKUs. For each SKU:
1. Pull trailing 30-day daily sales velocity (use amazon_product_detail
   and the historical data field).
2. Identify BSR trajectory over the last 90 days — climbing, flat, or
   declining.
3. Look up my category's seasonal multiplier for Q4 (Beauty=4.0,
   Home goods=2.5, Pet supplies=1.2, default=2.0 if not specified).
4. Compute target Q4 inventory = (velocity × multiplier × 90) + safety
   stock of 14 days.
5. Subtract inventory already in FBA and in-transit.
6. Output: SKU, target_inventory, current_in_FBA, in_transit,
   to_ship, priority (high/med/low).

Skip SKUs with trailing velocity under 0.5/day.
```

The screenshot below is the plan output for a sample 30-SKU catalog this week.

![Sorftime Seller Agent — Q4 inventory plan output](cover)

Total agent time: 12 seconds. Total time you spend: reading the priority list and approving shipments.

---

## What it does well

- **Time**: 2 hours → under 5 minutes including agent round-trip.
- **Reproducible**: same inputs, same plan. No "I forgot to include in-transit" mistakes.
- **Open source**: the agent is MIT licensed; you can audit the math.
- **Where it runs**: any MCP-compatible client — Claude Code, Cursor, Codex, OpenClaw.

## Where it falls short

The seasonal multiplier is hard-coded in the prompt. Real seasonal multipliers vary year over year (Q4 2025 was different from Q4 2024 for some categories), and the agent does not pull a live multiplier. You should review the multiplier values annually against your category's actual Q4 trajectory from prior years.

It also does not factor inbound shipping constraints. If you ship by air, your lead time is 7 days. If you ship by ocean, your lead time is 30-45 days. The plan tells you what to ship, not how to ship it — and the cut-off date for receiving inventory at Amazon FBA in time for Cyber Monday is mid-October, regardless of how much you ship.

---

## Building the Plan

The setup is the same one-liner as the other workflows:

```bash
git clone https://github.com/sorftime-ai/sorftime-seller-agent.git
cd sorftime-seller-agent
python3 scripts/install.py
```

Then save your SKU list and run the prompt above. The agent does the rest.

---

## Try it yourself

- GitHub: `git clone https://github.com/sorftime-ai/sorftime-seller-agent.git`
- Try the MCP server: https://open-intl.sorftime.com
- CLI quickstart: `python3 scripts/install.py`

The plan takes about three minutes from clone to first recommendation. Q4 inventory decisions are still yours.

---

## Sources

[1] Amazon Selling Partner API — sales and traffic reports (developer.amazonservices.com)
[2] Sorftime Seller Agent — verified MCP tools at open-intl.sorftime.com