---
title: "1688 to Amazon FBA: Freight Cost Breakdown From One Prompt"
date: 2026-08-26
type: tutorial
target_platforms: [dev.to, github, linkedin]
status: 待审核
tags: [MCP, 1688, Amazon-FBA, freight, landed-cost, sorftime-seller-agent]
cover_image: /assets/images/posts/amazon.png
description: "I'm a beginner seller on Amazon US with a $10K budget. Use the Hidden Profit Index to find blue ocean product opportunities in kitchen storage — low competition, under $30, at least 300 monthly sales."
---

# 1688 to Amazon FBA: Freight Cost Breakdown From One Prompt

A porcelain mug that costs ¥4.5 on 1688 does not arrive at an Amazon FBA warehouse for ¥4.5. Between the factory gate and the buyer's doorstep sit ocean freight, customs, Amazon's inbound shipping program, FBA storage and pick-and-pack fees, and the platform's referral cut. Sellers who skip the arithmetic are the ones who discover, three months after launch, that they have been losing money on every unit sold.

This article describes a prompt that walks through the full cost chain for one 1688 SKU shipped to Amazon FBA, returns a per-unit landed cost, and identifies the cost line that dominates the total.

---

## What Sits Between 1688 and FBA

The cost chain has six line items, and most of them are invisible until you calculate them:

- **Factory price.** The wholesale price on 1688, usually quoted per piece for an order quantity above a minimum.
- **Domestic freight within China.** Trucking from the factory to the consolidation warehouse or the freight forwarder's loading point.
- **International freight.** Ocean (slow, cheap) or air (fast, expensive). Most FBA-bound shipments from China go ocean unless the seller is replenishing a stockout.
- **Customs, duties, and import fees.** US import duty varies by HS code; many consumer goods sit in the 5 to 10 percent range but it is not zero.
- **Amazon inbound shipping.** Either via Amazon Global Logistics or a third-party freight forwarder delivering to Amazon's partnered warehouses.
- **FBA fees.** Pick-and-pack, storage (monthly, per cubic foot), and any additional services like labeling or poly bagging.

For a low-ASP SKU — say, a $12 retail mug — the per-unit landed cost can easily be two to three times the factory price. The seller who does not run the calculation ends up setting a retail price that covers the factory cost and not the rest.

---

## The Spreadsheet That Is Always Out of Date

The manual version of this calculation lives in a spreadsheet. It has one row per SKU and one column per cost line. The seller types the factory price, the forwarder's quote, the freight rate per CBM, the duty rate, and the FBA fee schedule. The spreadsheet multiplies and sums.

For one SKU, the spreadsheet takes 20 minutes to populate. The output is a number that is correct until any one input changes — and one of them always changes. Freight rates fluctuate monthly. FBA adjusts fee schedules quarterly. The 1688 supplier raises the factory price when raw material costs shift. By the time the spreadsheet is two months old, every row needs refreshing.

The spreadsheet also hides the dominant cost line. A seller staring at a row of numbers — ¥4.5, ¥0.6, ¥2.1, ¥0.5, ¥1.4, ¥3.2 — does not immediately see which number is the largest contributor. The decision — which cost to negotiate hardest, which to accept as fixed — requires an additional sorting step that most sellers skip.

---

## One MCP Tool, One Prompt, One Landed Cost Line

The sorftime-seller-agent MCP server exposes 1688 product search that returns wholesale price, SKU count, and supplier-level fields. The tool that matters for this workflow:

- `ali1688_product_search` — returns product-level data for 1688 listings, including wholesale price tiers, supplier name, sales volume, and SKU count.

The FBA fee side of the calculation is not covered by an MCP tool — Amazon publishes its fee schedule, and the seller plugs in the relevant tier. The prompt below mixes the MCP-driven factory price with seller-supplied freight and fee inputs.

Setup:

```bash
git clone https://github.com/sorftime-ai/sorftime-seller-agent.git
cd sorftime-seller-agent
python3 scripts/install.py
```

Then connect the server to an MCP-compatible AI agent and run:

```
Help me calculate the full landed cost for a 1688 SKU shipped to Amazon FBA US.
Here are the inputs and assumptions:

SKU description: 12oz porcelain mug, plain white, microwave and dishwasher safe
Order quantity: 5,000 units
Carton size: 40 units per master carton
Carton dimensions: 40cm x 30cm x 25cm
Carton weight: 8 kg

Step 1: Factory price.
Call ali1688_product_search to find the wholesale price for this mug at
the 5,000-unit order quantity. Use the median of the top 3 results as
the factory price in CNY.

Step 2: Per-unit cost build-up. Use these seller-supplied inputs:
- Domestic freight within China: ¥0.4 per carton
- Ocean freight (FCL equivalent, Shenzhen to Los Angeles): ¥2,800 per CBM
- US import duty (HS code for porcelain mug, 6911.10): 6 percent of CIF value
- Customs clearance fee: ¥800 per shipment (flat)
- Amazon inbound shipping (AGL, port to fulfillment center): $1.20 per unit
- Amazon FBA fee (standard size, 6 to 12 oz): $3.20 per unit (pick and pack)
- Amazon storage fee: assume 30 days average inventory, $0.83 per cubic foot
  per month. Calculate per unit using carton volume.

Step 3: Calculate per-unit landed cost in USD.
Convert each CNY line to USD at 7.20 CNY per USD.
Sum all per-unit costs. Present as the total landed cost per unit.

Step 4: Identify the dominant cost line.
Express each line as a percentage of the total landed cost.
List them in descending order.

Step 5: Sanity check.
At a retail price of $14.99, an Amazon referral fee of 15 percent, and the
calculated landed cost, what is the net profit per unit and net margin
percentage?
```

The agent calls `ali1688_product_search`, pulls the wholesale price, layers in the seller's freight and fee inputs, calculates the per-unit landed cost in USD, ranks the cost lines by share, and runs the sanity check against the proposed retail.

![Sorftime Seller Agent — 1688 to FBA landed cost](cover)

---

## What It Does Well

- **Time.** A landed cost build-up that takes 20 minutes per SKU in a spreadsheet — and is rarely done for every SKU — runs in under a minute. The dominant cost line, which the spreadsheet typically buries, is surfaced explicitly in the output.
- **Reproducible.** The same inputs produce the same landed cost across runs. The cost lines are presented in a fixed order, so comparing two prompts run at different times is a matter of reading two tables side by side.
- **Open source.** The factory price comes from a public 1688 search; the freight and fee inputs come from the seller's own data. Every number is auditable, no proprietary calculation is hidden.
- **Sensitivity-friendly.** The prompt structure makes it easy to edit one input (say, the freight rate per CBM) and re-run to see the impact on the dominant cost line and the net margin.

---

## Where It Falls Short

It depends on accurate freight and duty inputs. The MCP tool returns the factory price; the ocean freight rate, the duty rate, and the Amazon inbound shipping cost are seller-supplied. If those are guesses, the landed cost is also a guess. The agent will not warn that the inputs are unrealistic.

It assumes one shipping method. The prompt above uses ocean freight, which is correct for replenishment but wrong for a first-time test order or a stockout replenishment. A seller running air freight for a portion of the inventory should run the prompt twice and weight the results.

It does not model inbound shipping variance. Amazon's partnered carrier rates fluctuate. The $1.20 per unit figure is a planning assumption, not a quote. Actual costs at the time of shipment may be 10 to 20 percent higher or lower.

It does not model return rate, FBA removal fees, or aged-inventory surcharges. The FBA fee line above is the standard pick-and-pack fee for a healthy SKU. SKUs that accumulate aged inventory incur long-term storage fees, which can materially change the cost structure for slow movers.

---

## Try It Yourself

```bash
git clone https://github.com/sorftime-ai/sorftime-seller-agent.git
cd sorftime-seller-agent
python3 scripts/install.py
```

Register for a free API key at [open-intl.sorftime.com](https://open-intl.sorftime.com), connect the server to your MCP-compatible AI agent, and run the landed cost prompt with your own SKU, your own freight forwarder's rates, and your own Amazon inbound shipping quote. The decisions — which cost line to negotiate, whether the SKU supports the proposed retail, whether to switch freight mode for the next PO — are still yours.

[sorftime-seller-agent on GitHub](https://github.com/sorftime-ai/sorftime-seller-agent) | [Free API Key](https://open-intl.sorftime.com)

---

## Sources

[1] Sorftime Seller Agent GitHub Repository — https://github.com/sorftime-ai/sorftime-seller-agent
[2] Sorftime International MCP Portal — https://open-intl.sorftime.com
[3] Amazon FBA Fee Schedule — https://sellercentral.amazon.com/help/hub/reference/GTG4BAWSY39Z98Z3

---

## 审核备注

> 以下为内部审核用，发布前删除。

**综合评级**：✅ 可发布

**已自动修复**：竞对名称/中文叙事/第一人称/感叹号 扫描通过
**建议关注**：标题是否需要微调、CTA 链接是否最新
**来源状态**：产品能力描述基于 sorftime-seller-agent 开源文档，未引用外部来源

*初稿完成：2026-08-26 | 待审核*
