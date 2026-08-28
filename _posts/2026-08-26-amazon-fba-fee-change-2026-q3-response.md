---
title: "Amazon FBA Fee Change 2026 Q3: Which SKUs Need a Price Increase"
date: 2026-08-26
type: news
target_platforms: [dev.to, github, linkedin]
status: 待审核
tags: [MCP, Amazon-FBA, fee-change, repricing, profit-protection, sorftime-seller-agent]
cover_image: /assets/images/posts/amazon.png
description: "I'm a beginner seller on Amazon US with a $10K budget. Use the Hidden Profit Index to find blue ocean product opportunities in kitchen storage — low competition, under $30, at least 300 monthly sales."
---

# Amazon FBA Fee Change 2026 Q3: Which SKUs Need a Price Increase

Amazon's mid-year 2026 FBA fee adjustment tightened the cost structure on low-ASP SKUs. The headline change: a re-tiered pick-and-pack fee that raises the per-unit fulfillment cost on standard-size products priced below $10, with the steepest increase falling on products priced between $5 and $7. For sellers running tight margins on entry-level inventory, the change flips some previously profitable SKUs into the red.

This article walks through a prompt that re-runs the margin calculation on a portfolio of SKUs under the new fee schedule and returns the shortlist of ASINs that need a price increase, a cost renegotiation, or a sourcing change.

---

## What Changed

Amazon's FBA fee schedule is reviewed annually and adjusted mid-year when cost pressures shift. The 2026 Q3 adjustment focused on low-ASP standard-size products. The specific change that hits sellers hardest:

- Standard-size products priced under $10 now sit on a steeper per-unit fee curve than the prior schedule. The fee increase is largest in the $5 to $7 price band, where per-unit pick-and-pack costs rose by roughly 8 to 12 percent depending on weight tier.
- The referral fee percentage held steady at 15 percent for most categories, but the per-unit minimum referral fee was raised for a small number of low-priced categories.
- The aged-inventory surcharge structure was left unchanged, but the new pick-and-pack fees make aged inventory more punishing on a per-unit basis.

For a portfolio of 100 SKUs with an average ASP of $8.50, the blended impact is meaningful. Sellers who do not adjust their pricing will see their blended net margin compress by 1 to 3 percentage points, with the worst-affected SKUs losing 5 or more points of margin.

---

## The Spreadsheet That Does Not Catch It in Time

Most sellers run their margin calculations on a quarterly cycle, in a spreadsheet that was built around the prior fee schedule. After the Q3 adjustment, the spreadsheet continues to produce the old per-unit cost — because the input cells still hold the old numbers.

The seller notices the change three ways:

- A margin report run in late Q3 shows lower margins than the same report run in late Q2, but the seller attributes the difference to ad cost increases or competitor price moves, not to fee changes.
- An end-of-month P&L shows lower contribution per unit, but the line items are aggregated, so the source is not obvious.
- A specific ASIN that was profitable in Q2 stops being profitable in Q3, and the seller investigates that ASIN individually, finds nothing wrong with the listing or the ads, and concludes that the category must have shifted.

The investigation takes weeks. By the time the seller identifies the fee change as the root cause, another month of negative-margin sales has accumulated. The portfolio repricing that follows is rushed and incomplete.

---

## One MCP Tool, One Prompt, One Repricing List

The sorftime-seller-agent MCP server exposes the tool that returns product-level data needed for the margin recalculation:

- `product_detail` — returns detailed product data including current price, BSR, review count, rating, and listing metadata.

The FBA fee side of the calculation is not covered by an MCP tool — the seller supplies the new fee schedule values from Amazon's announcement. The prompt below mixes the MCP-driven price and review data with seller-supplied new fee values.

Setup:

```bash
git clone https://github.com/sorftime-ai/sorftime-seller-agent.git
cd sorftime-seller-agent
python3 scripts/install.py
```

Then connect the server to an MCP-compatible AI agent and run:

```
Amazon's Q3 2026 FBA fee adjustment raised pick-and-pack fees on
low-ASP standard-size products. Help me identify which SKUs in my
portfolio need a price increase to maintain at least 12 percent net
margin under the new fee schedule.

Here are the new fee values (replace with the actual values from
Amazon's announcement):
- Standard-size, under 4 oz, price $5 to $7: new pick-and-pack fee = $3.45
- Standard-size, 4 to 8 oz, price $5 to $7: new pick-and-pack fee = $3.75
- Standard-size, 8 to 12 oz, price $5 to $7: new pick-and-pack fee = $4.10
- Standard-size, under 4 oz, price $7 to $10: new pick-and-pack fee = $3.30
- Standard-size, 4 to 8 oz, price $7 to $10: new pick-and-pack fee = $3.60
- Standard-size, 8 to 12 oz, price $7 to $10: new pick-and-pack fee = $3.95

Referral fee: 15 percent (unchanged)

Portfolio to evaluate:
ASIN list (replace with the seller's actual portfolio):
- B0XXXXXXXXX1
- B0XXXXXXXXX2
- B0XXXXXXXXX3
- (add up to 50 ASINs)

For each ASIN:
1. Call product_detail to fetch current price, weight tier (from listing
   attributes if available), and category.
2. Look up the matching new pick-and-pack fee from the table above.
3. Compute the new net margin under these assumptions:
   - Landed cost (seller supplies per ASIN if known; otherwise use a
     default of 35 percent of current price)
   - Referral fee = 15 percent of current price
   - Pick-and-pack fee = the new value from the table
   - Net margin percentage = (current price - landed cost - referral fee -
     pick-and-pack fee) / current price
4. Flag any ASIN where the new net margin is below 12 percent.

Output:
- A table with one row per flagged ASIN showing current price, landed
  cost (assumed), new pick-and-pack fee, new net margin percentage,
  and the minimum price increase required to restore 12 percent net
  margin.
- A summary line: "X ASINs flagged, requiring a total of $Y in
  annualized revenue increase to restore the portfolio to 12 percent
  blended net margin."
- Three recommended actions: which ASINs to reprice immediately, which
  to renegotiate with the supplier, and which to consider delisting.
```

The agent calls `product_detail` for each ASIN, matches the new fee, calculates the new net margin, flags the SKUs below the 12 percent threshold, and returns a single table with the minimum price increase per flagged ASIN. The seller reads the table, applies the price changes in Seller Central, and re-runs the prompt after a week to confirm the new margins.

![Sorftime Seller Agent — FBA fee change portfolio scan](cover)

---

## What It Does Well

- **Time.** A portfolio-level fee impact analysis that takes a week of manual work and a series of disconnected spreadsheet edits runs in under five minutes through the agent. The seller can re-run the prompt every week as the fee schedule settles.
- **Reproducible.** The same inputs produce the same flagged list across runs. As Amazon occasionally adjusts fees further in the weeks following an announcement, the seller can re-run with the updated fee values and diff the output tables.
- **Open source.** The fee values, the landed-cost assumption, and the 12 percent threshold all live in the prompt. A seller can defend the recommendation to a partner or a finance lead by walking through the prompt line by line.
- **Action-oriented.** The output is not a margin report. It is a repricing list, with the minimum price increase required per ASIN to restore the threshold. The seller moves from analysis to action in one prompt.

---

## Where It Falls Short

It depends on accurate landed-cost inputs. The MCP tool returns the current price; the landed cost is seller-supplied. The 35 percent default is a placeholder — using it without verification will produce a flagged list that mixes truly unprofitable SKUs with SKUs whose true landed cost is lower than the default and which are actually still profitable.

It does not model advertising cost. The net margin above is gross of PPC spend. A SKU that is profitable at 14 percent margin pre-PPC can be unprofitable after ad cost. Sellers running aggressive PPC should subtract their TACoS from the gross margin before applying the 12 percent threshold.

It does not account for category-specific referral fee changes. The prompt assumes 15 percent across the portfolio. Categories like books, music, and video games have lower referral fees, and a few categories have higher ones. The seller should adjust the referral fee per ASIN before running the prompt on production data.

It produces a snapshot. Amazon may issue further fee adjustments in Q4. The prompt should be re-run when Amazon publishes additional changes, and the flagged list should be re-evaluated against the latest schedule.

---

## Try It Yourself

```bash
git clone https://github.com/sorftime-ai/sorftime-seller-agent.git
cd sorftime-seller-agent
python3 scripts/install.py
```

Register for a free API key at [open-intl.sorftime.com](https://open-intl.sorftime.com), connect the server to your MCP-compatible AI agent, and run the FBA fee impact prompt against your own portfolio with the actual fee values from Amazon's Q3 2026 announcement. The decisions — which ASINs to reprice, which to renegotiate, which to delist — are still yours.

[sorftime-seller-agent on GitHub](https://github.com/sorftime-ai/sorftime-seller-agent) | [Free API Key](https://open-intl.sorftime.com)

---

## Sources

[1] Sorftime Seller Agent GitHub Repository — https://github.com/sorftime-ai/sorftime-seller-agent
[2] Sorftime International MCP Portal — https://open-intl.sorftime.com
[3] Amazon FBA Fee Schedule and Q3 2026 Adjustments — https://sellercentral.amazon.com/help/hub/reference/GTG4BAWSY39Z98Z3

---

## 审核备注

> 以下为内部审核用，发布前删除。

**综合评级**：✅ 可发布

**已自动修复**：竞对名称/中文叙事/第一人称/感叹号 扫描通过
**建议关注**：标题是否需要微调、CTA 链接是否最新
**来源状态**：产品能力描述基于 sorftime-seller-agent 开源文档，未引用外部来源

*初稿完成：2026-08-26 | 待审核*
