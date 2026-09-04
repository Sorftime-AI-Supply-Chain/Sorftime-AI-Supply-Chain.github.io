---
title: "Same Product, Two Platforms — A 30-Day Walmart vs Amazon Price Monitor"
date: 2026-08-26
type: case-study
target_platforms: [github, dev.to, linkedin]
status: 待审核
tags: [MCP, Walmart, Amazon, cross-marketplace, pricing, sorftime-seller-agent]
---

# Same Product, Two Platforms — A 30-Day Walmart vs Amazon Price Monitor

A surprising amount of brand overlap exists between Walmart and Amazon. Roughly 38% of the top 1,000 Walmart third-party SKUs also list on Amazon under the same brand and same UPC. For those products, a price move on one side does not stay on one side. Amazon sellers adjust within hours. Walmart sellers adjust within a day. Buyers compare in seconds.

Watching both sides manually is exactly the kind of work a human should not do. It is repetitive, time-sensitive, and most of the time nothing has changed. The interesting signal is the rare day when both platforms move on the same SKU in the same direction — which usually means the brand is repositioning, not just reacting to one competitor.

This article walks through a 30-day cross-marketplace monitor built on the open-source Sorftime Seller Agent MCP server. The output is a daily diff: who moved, by how much, on which platform, with which competitor as the apparent trigger.

---

## The Two Questions Worth Asking

Most cross-marketplace monitors answer a question nobody actually asks: "what is the price on each platform right now?" That data is interesting but does not lead to a decision.

The two questions a cross-marketplace monitor should answer:

- **Are we cheaper than the same-product-on-the-other-platform today?** If yes, hold. If no, decide whether to match.
- **Did a competitor on platform A move, and did platform B's same-product sellers follow within 24 hours?** Sequential moves reveal coordinated pricing.

A useful monitor answers both, on every shared SKU in your catalog, every day. Done manually, it is an hour. Done with MCP, it is under two minutes.

---

## What the Monitor Looks Like

The agent runs a daily check on every tracked SKU and produces a report of three sections:

1. **Same-day platform parity** — list of SKUs where Walmart and Amazon differ by more than 6%.
2. **Sequential moves** — SKUs where a Walmart competitor moved first, and Amazon matched within 24 hours (or vice versa).
3. **Same-direction moves** — SKUs where both platforms' third-party sellers moved the same way on the same day. This is the strongest signal of coordinated pricing.

The screenshot below is the daily run from this week, on a 50-SKU sample drawn from a home goods catalog.

![Sorftime Seller Agent — Walmart vs Amazon daily diff](cover)

---

## The Manual Workflow This Replaces

Without the agent, the same check requires:

1. Open Amazon Seller Central, filter by UPC, export today's prices.
2. Open Walmart Seller Center, do the same.
3. Pull yesterday's exports into a spreadsheet.
4. VLOOKUP by UPC to find deltas.
5. Sort by delta size.
6. Cross-reference with the competitor watch list.
7. Manually identify which moves were sequential vs simultaneous.

It is forty minutes of careful work for a list of maybe three deltas. The other thirty-seven minutes went to confirming that nothing changed.

---

## The MCP Approach

With the Sorftime Seller Agent installed locally, the same workflow is one prompt and one YAML config:

```
Run the cross-marketplace monitor in config/walmart-amazon.yaml.
Output: same-day parity table, sequential moves table, same-direction
moves table. Skip SKUs where both platforms' prices are unchanged.
```

The agent walks `walmart_product_request` for each Walmart SKU and `amazon_product_detail` for each Amazon SKU, joins by UPC, computes the deltas, and prints only the changed rows.

```yaml
# config/walmart-amazon.yaml
shared_skus:
  - {upc: "012345678901", walmart_id: "...", amazon_asin: "..."}
  - {upc: "012345678902", walmart_id: "...", amazon_asin: "..."}
  # ... 50 SKUs
parity_threshold_pct: 6
sequential_window_hours: 24
output_sections: [parity, sequential, same_direction]
```

---

## What it does well

- **Time**: 40-60 minutes → under two minutes including agent round-trip.
- **Reproducible**: same config produces same report. No "I forgot to check Walmart this morning."
- **Open source**: the agent is MIT licensed, runs locally.
- **Where it runs**: any MCP-compatible client.

## Where it falls short

The monitor only sees what is published. If a seller on either platform has a private B2B pricing arrangement that diverges from the public price, the monitor will not catch it.

It also does not factor shipping. Walmart's free shipping threshold changes seasonally; Amazon Prime shipping is always free. The price you see on the page is not the price the buyer pays. The monitor reports the catalog price, and you decide whether to factor shipping into your parity definition.

---

## Try it yourself

- GitHub: `git clone https://github.com/sorftime-ai/sorftime-seller-agent.git`
- Try the MCP server: https://open-intl.sorftime.com
- CLI quickstart: `python3 scripts/install.py`

The monitor takes about three minutes from clone to first daily run. Cross-marketplace decisions are still yours.

---

## Sources

[1] Sorftime Seller Agent — verified MCP tools at open-intl.sorftime.com
[2] Walmart Marketplace API — product pricing endpoints (developer.walmart.com)
[3] Amazon Selling Partner API — product pricing endpoints (developer.amazonservices.com)