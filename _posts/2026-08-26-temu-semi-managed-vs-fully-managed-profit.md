---
title: "TEMU Semi-Managed vs Fully-Managed: A Profit Calculation Prompt"
date: 2026-08-26
type: tutorial
target_platforms: [dev.to, github, linkedin]
status: 待审核
tags: [MCP, TEMU, semi-managed, fully-managed, profit-calculation, sorftime-seller-agent]
cover_image: /assets/images/posts/temu.png
description: "Search for LED strip lights on TEMU US. Show me the top products by cumulative sales — include price, rating, store name, and whether it's semi-managed or marketplace."
---

# TEMU Semi-Managed vs Fully-Managed: A Profit Calculation Prompt

TEMU sellers in 2026 increasingly face a structural choice: sell under the fully-managed model where TEMU controls pricing, logistics, and most of the buyer relationship, or under the semi-managed model where the seller controls inbound shipping, sets the price, and keeps more of the margin. The two paths produce meaningfully different net margins on the same SKU. Most sellers do not realize by how much until they sit down and do the math.

This article walks through a prompt that compares the two models on a specific SKU, using TEMU product data and one MCP server, and surfaces the net margin delta that should drive the operational decision.

---

## Why the Choice Matters

The fully-managed model removes most operational burden. TEMU handles pricing, the storefront, customer service, returns logistics, and last-mile shipping. The seller ships bulk inventory to a TEMU warehouse, names a wholesale cost, and accepts the payout. The tradeoff is that TEMU's algorithm sets the retail price and the platform fee is significant — net margins in the low single digits are common for fully-managed sellers.

The semi-managed model flips the trade. The seller controls retail pricing, ships directly to the buyer from their own warehouse (often via a 3PL), and keeps more of the margin. The tradeoff is operational: customer service, returns, and shipping cost variability all sit with the seller. Net margins can be two to four times higher for the same SKU, but only if the seller's cost structure supports it.

For a SKU priced at $15 retail, the difference between 5 percent net and 12 percent net is the difference between a hobby and a business. Knowing which model wins on which SKU — and why — is the question.

---

## The Manual Comparison That Misses the Subtlety

To compare the two models manually for one SKU:

1. Open TEMU Seller Central. Pull the SKU's current wholesale cost under fully-managed terms. Note the platform fee percentage and any category-specific deductions.
2. Open the same SKU under semi-managed terms. Pull the seller's landed cost, the suggested retail, and the platform fee percentage for semi-managed listings.
3. Layer in shipping cost. Fully-managed usually quotes a flat inbound shipping cost per unit. Semi-managed requires the seller to estimate per-order outbound shipping, often using a 3PL rate card.
4. Layer in return rate. Fully-managed returns are absorbed by TEMU. Semi-managed returns are absorbed by the seller — typically 5 to 12 percent of orders for most consumer goods.
5. Compute net margin under each model:
     fully_managed_net = retail_price * (1 - platform_fee) - wholesale_cost - inbound_shipping
     semi_managed_net = retail_price * (1 - platform_fee) - landed_cost - outbound_shipping * (1 + return_rate)
6. Compute net margin percentage relative to retail price for each model.

For one SKU, this takes 30 to 45 minutes if the data is clean. The output is one number per model. The decision is whether to relist the SKU under the higher-margin path — which requires operational changes, not just a spreadsheet calculation.

The bottleneck is data assembly. TEMU exposes most of the fields through Seller Central, but the return rate and outbound shipping cost have to come from the seller's own operational records. The comparison breaks when those numbers are wrong.

---

## One MCP Tool, One Prompt, One Net Margin Table

The sorftime-seller-agent MCP server exposes TEMU search tools that return product-level fields including cost and price indicators. The relevant tool:

- `temu_product_search` — returns product-level data for TEMU listings, including selling price, cumulative sales, monthly sales, and listing metadata.

For this workflow the prompt does not call the MCP tool once and stop. It uses TEMU product data as the basis for the comparison and feeds the seller's own operational inputs (shipping cost, return rate, platform fee percentage) into the calculation.

Setup:

```bash
git clone https://github.com/sorftime-ai/sorftime-seller-agent.git
cd sorftime-seller-agent
python3 scripts/install.py
```

Then connect the server to an MCP-compatible AI agent and run:

```
Help me compare the fully-managed vs semi-managed model on a specific TEMU
SKU. Here are the inputs:

SKU: portable-blender-PB06 (TEMU US)
Selling price (US retail): $24.99
Fully-managed wholesale cost (TEMU's quoted payout basis): $14.50
Fully-managed platform fee: 18 percent of retail
Fully-managed inbound shipping cost (per unit): $1.80

Semi-managed landed cost (manufacturing + inbound to my 3PL): $7.20
Semi-managed platform fee: 8 percent of retail
Semi-managed outbound shipping (per order, 3PL rate): $3.40
Estimated return rate: 8 percent of orders
Other variable costs under semi-managed (packaging, customer service per order): $0.60

Tasks:
1. For each model, calculate:
     a. Net revenue per unit after platform fee
     b. Total variable cost per unit (including the probability-weighted
        return cost for semi-managed)
     c. Net profit per unit
     d. Net profit margin as a percentage of retail price
2. Present both side by side in a clear table.
3. Run a sensitivity check: re-run the semi-managed calculation with
   return rates of 4 percent, 8 percent, and 12 percent. Show how the
   margin changes.
4. Run a second sensitivity check: re-run the semi-managed calculation
   with outbound shipping of $2.80, $3.40, and $4.00.
5. State explicitly: at what return rate does semi-managed margin drop
   below fully-managed margin? At what outbound shipping cost does the
   same crossover happen?
6. Recommend: under the base case inputs, which model wins, and by how
   much in absolute dollars per 1000 units sold?
```

The agent runs the arithmetic, presents the side-by-side, runs the two sensitivity sweeps, identifies the crossover points, and returns a recommendation in absolute dollars per 1000 units. The recommendation is a starting point. The seller weighs operational fit.

![Sorftime Seller Agent — TEMU managed-model margin comparison](cover)

---

## What It Does Well

- **Time.** A comparison that takes 30 to 45 minutes by hand — and is prone to arithmetic error in the return-weighted cost step — runs in under a minute through the agent. The sensitivity sweeps are the part that almost no seller does by hand but should.
- **Reproducible.** The same inputs produce the same outputs across runs. Re-running the calculation after a cost change is a matter of editing one number in the prompt.
- **Open source.** The math, the crossover logic, and the sensitivity structure are visible in the prompt. A seller can defend the recommendation in front of a partner or an investor by walking through the prompt line by line.
- **Cross-model on one screen.** Fully-managed and semi-managed assumptions often live in different documents — TEMU Seller Central for one, the seller's own cost sheet for the other. The prompt pulls them into one calculation.

---

## Where It Falls Short

It depends on the seller supplying accurate operational inputs. The MCP tool cannot know the seller's outbound shipping rate or historical return rate. If those inputs are guesses, the crossover points the agent surfaces are also guesses.

It does not model fixed costs. The comparison above is variable-cost-only. It does not include warehouse rent, 3PL monthly fees, customer service headcount, or TEMU's promotional co-funding. A seller running high fixed costs under semi-managed may find the net margin advantage is consumed by overhead.

It does not account for sales velocity differences. Fully-managed listings often rank higher in TEMU search and convert better than semi-managed listings in the same category. A 12 percent margin on a higher-velocity fully-managed SKU can outperform a 15 percent margin on a slower semi-managed SKU. The model above assumes equal volume; reality rarely does.

It does not handle category-specific platform fee changes. TEMU adjusts platform fees by category and by seller tier. The 18 percent and 8 percent numbers in the example should be replaced with the seller's actual fee schedule before the prompt is run on production data.

---

## Try It Yourself

```bash
git clone https://github.com/sorftime-ai/sorftime-seller-agent.git
cd sorftime-seller-agent
python3 scripts/install.py
```

Register for a free API key at [open-intl.sorftime.com](https://open-intl.sorftime.com), connect the server to your MCP-compatible AI agent, and run the margin comparison prompt with your own SKU, your own costs, and your own return rate. The decision — which model to operate under for each SKU, where to accept lower margin for lower operational burden, where to push for higher margin at higher operational cost — is still yours.

[sorftime-seller-agent on GitHub](https://github.com/sorftime-ai/sorftime-seller-agent) | [Free API Key](https://open-intl.sorftime.com)

---

## Sources

[1] Sorftime Seller Agent GitHub Repository — https://github.com/sorftime-ai/sorftime-seller-agent
[2] Sorftime International MCP Portal — https://open-intl.sorftime.com
[3] TEMU Seller Central — https://seller.kuajingmaihuo.com

---

## 审核备注

> 以下为内部审核用，发布前删除。

**综合评级**：✅ 可发布

**已自动修复**：竞对名称/中文叙事/第一人称/感叹号 扫描通过
**建议关注**：标题是否需要微调、CTA 链接是否最新
**来源状态**：产品能力描述基于 sorftime-seller-agent 开源文档，未引用外部来源

*初稿完成：2026-08-26 | 待审核*
