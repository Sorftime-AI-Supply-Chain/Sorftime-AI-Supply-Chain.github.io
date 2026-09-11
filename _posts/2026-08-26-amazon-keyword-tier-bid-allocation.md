---
title: "Amazon Keyword Tiering: Core, Long-Tail, and Competitor Bid Allocation"
date: 2026-08-26
type: tutorial
target_platforms: [dev.to, github, linkedin]
status: 待审核
tags: [Amazon, PPC, keyword-strategy, bid-allocation, sorftime-seller-agent]
---

# Amazon Keyword Tiering: Core, Long-Tail, and Competitor Bid Allocation

Most Amazon advertisers set keyword bids in a single bucket. They look at the suggested bid, adjust up or down by feel, and move on. The result is a portfolio where high-converting core terms get underbid while low-converting long-tail terms get overbid, and the account's overall ACoS drifts upward as budget gets spent inefficiently.

The fix is structural. Sort the account's keywords into three tiers — core, long-tail, and competitor — and allocate bid budgets to each tier based on conversion economics. The tiering is not a one-time setup. It is a monthly recalibration against actual performance data.

This article walks through how to define the three tiers, allocate bid budgets across them, and embed each tier into both the PPC campaign and the product listing.

![Sorftime Seller Agent — Keyword Tier Bid Allocation](cover)

---

## Why One Bucket Fails

A keyword bid is a bet on conversion rate × AOV × profit margin. The bet is more accurate when conversion data is high and less accurate when conversion data is low. Sellers who treat all keywords the same are implicitly assuming that all keywords convert at the same rate, which is rarely true.

The data shows the opposite:

- **Core keywords** (1-3 word phrases with high search volume) typically convert at 3-8 percent on established ASINs. The volume is high. The conversion rate is moderate. The competition for these keywords is intense and bids are elevated.

- **Long-tail keywords** (4+ word phrases with lower search volume) typically convert at 8-15 percent. The volume is lower. The conversion rate is higher. The competition is lower and bids are typically 40-60 percent below core keyword bids.

- **Competitor keywords** (brand names of competing products) convert at widely variable rates. Some convert above 15 percent when the competitor's product is out of stock or overpriced. Some convert below 2 percent when the competitor has strong brand loyalty. Each competitor keyword needs its own data.

A single bid bucket flattens these differences. A seller who bids $1.00 across all three tiers overpays on long-tail and underpays on core. The result is wasted spend on low-converting queries and missed impressions on high-converting queries.

---

## Defining the Three Tiers

### Tier 1: Core Keywords

Core keywords are the head terms that define the category. For a stainless steel water bottle ASIN, core keywords include "water bottle," "stainless steel water bottle," "insulated water bottle," and "reusable water bottle."

Definition:

- 1-3 word phrases
- High monthly search volume (typically 50,000+)
- Direct category match to the ASIN
- The keywords a shopper would type when beginning their search

Bid behavior:

- Higher absolute bids ($1.50-3.50 for competitive categories)
- Higher budget share (typically 40-50 percent of total PPC budget)
- Continuous optimization to maintain top of search placement

### Tier 2: Long-Tail Keywords

Long-tail keywords are specific phrases that indicate stronger purchase intent. For the water bottle example: "stainless steel water bottle 32 oz with straw," "leak proof water bottle for gym," "dishwasher safe insulated water bottle."

Definition:

- 4+ word phrases
- Lower monthly search volume (typically 1,000-50,000)
- Specific feature, use case, or constraint mentioned
- The keywords a shopper types when narrowing their search

Bid behavior:

- Lower absolute bids ($0.50-1.50)
- Higher budget share (typically 30-40 percent of total PPC budget) due to better conversion economics
- Less competitive pressure, easier to maintain placement

### Tier 3: Competitor Keywords

Competitor keywords are brand names of other products or companies in the category. For the water bottle ASIN: "Hydro Flask," "Yeti Rambler," "Owala FreeSip."

Definition:

- Brand names of specific competing products
- Variable conversion rate (must be measured per competitor)
- Often categorized separately because they imply brand-loyal shopper behavior

Bid behavior:

- Highly variable bids ($0.30-3.00 depending on conversion data)
- Lower budget share (typically 15-25 percent of total PPC budget)
- Per-competitor performance tracking required
- Higher risk of trademark policy violation (Amazon restricts bidding on trademarks in some categories)

---

## The Bid Allocation Model

The bid allocation across tiers should follow the conversion economics. A simplified allocation:

```
Tier 1 (Core):        45% of budget, target ACoS 25-35%
Tier 2 (Long-Tail):   35% of budget, target ACoS 15-20%
Tier 3 (Competitor):  20% of budget, target ACoS 30-45%
```

The ACoS targets reflect the conversion economics: core keywords convert at lower rates, long-tail at higher rates, competitor keywords are variable. The total budget weighted ACoS lands around 25 percent, which is the typical target for a healthy Sponsored Products portfolio.

The allocation is not rigid. A SKU in a highly competitive category might run 55 percent on core. A SKU with strong long-tail performance might run 45 percent on long-tail. The model is the starting point; the data drives the adjustment.

---

## Embedding Keywords Into the Listing

The PPC bid is only half the optimization. The keywords also need to be present in the listing for organic ranking. Amazon's algorithm rewards listings where the paid keyword and the organic listing match — a high-PPC keyword with strong organic ranking converts better than a high-PPC keyword with weak organic ranking.

The embedding allocation:

| Tier | Listing Placement |
|---|---|
| Core (Tier 1) | Title, first 2 bullets |
| Long-Tail (Tier 2) | Bullets 3-5, product description, A+ Content |
| Competitor (Tier 3) | Backend search terms (only — never visible in listing) |

Why this distribution:

- **Core keywords in title and first bullets.** The shopper scanning the listing needs to see the core category match in the first 200 characters. Embedding core terms in the title drives both click-through and conversion.

- **Long-tail keywords in remaining bullets and A+.** The shopper who is reading bullets 3-5 has narrowed their search. Specific feature mentions (size, use case, compatibility) match their long-tail queries.

- **Competitor keywords in backend only.** Amazon restricts the use of competitor brand names in customer-facing copy. Backend search terms allow the algorithm to match competitor queries without violating trademark policy.

---

## The Monthly Recalibration Cycle

Tier performance shifts month over month. The recalibration cycle:

1. **Pull 30-day performance data** for every keyword in the account: impressions, clicks, conversions, ACoS, conversion rate.

2. **Sort by tier** and calculate average ACoS, conversion rate, and total spend per tier.

3. **Identify promotion candidates** — long-tail keywords with ACoS below tier target and conversion rate above tier median. Promote these to higher bids or move from phrase match to exact match.

4. **Identify pause candidates** — keywords with ACoS above 100 percent and conversion rate below 0.5 percent. Pause these to free budget for promotion candidates.

5. **Reallocate budget** across tiers based on updated performance. The 45/35/20 split is a starting point; the actual split should follow the data.

6. **Update listing embedding** as new core keywords emerge (typically 1-2 per quarter).

A seller who runs the cycle monthly keeps the keyword portfolio aligned with actual performance. A seller who runs it quarterly or never runs it lets the portfolio drift toward inefficiency.

---

## Common Pitfalls

### Treating Brand-Defensive Keywords as Conversion Plays

Bidding on competitor brand keywords is a brand-defensive move, not a conversion play. The conversion rate on competitor terms is structurally lower because the shopper is already leaning toward the competitor. The bid is justified by the visibility into the competitor's search results, not by direct conversion.

### Over-Reliance on Suggested Bids

Amazon's suggested bid is a category average. It does not know your conversion rate. Sellers who accept the suggested bid across the board overpay on long-tail keywords (where category-average bid is too high) and underpay on core keywords (where the category-average bid is competitive, not dominant).

### Single Match Type Strategy

A portfolio that runs all keywords on broad or all on exact misses optimization. The optimal structure is exact match on confirmed converters, phrase match on promising but unproven keywords, and broad match on discovery keywords with tight negative keyword lists.

### Ignoring Negative Keywords

A portfolio without negative keywords wastes 20-40 percent of budget on irrelevant queries. The negative keyword list should be reviewed weekly and updated based on search term reports.

---

## Using MCP to Model the Tier Allocation

The sorftime-seller-agent MCP server can pull Amazon PPC performance data, sort keywords into tiers, and recommend the budget reallocation in a single prompt.

```bash
git clone https://github.com/DannylydST/sorftime-seller-agent
cd sorftime-seller-agent
python3 scripts/install.py
```

The tier allocation prompt:

```
Build a keyword tier allocation model for my Amazon PPC account.

Inputs:
- My seller account: <account ID>
- 30-day performance data per keyword: keyword, impressions, clicks,
  conversions, ACoS, conversion rate, current bid, match type

Tasks:
1. Classify every keyword into one of three tiers:
   - Tier 1 (Core): 1-3 word phrases, high volume
   - Tier 2 (Long-Tail): 4+ word phrases, lower volume
   - Tier 3 (Competitor): brand names of other products

2. For each tier, calculate:
   - Total spend
   - Total sales
   - Average ACoS
   - Average conversion rate
   - Percentage of total spend

3. Compare the actual tier allocation to the target 45/35/20 split.
   Identify tiers over- or under-allocated.

4. For each keyword, recommend one of these actions:
   - Increase bid (conversion rate above tier median, ACoS below target)
   - Decrease bid (conversion rate below tier median, ACoS above target)
   - Pause (ACoS above 100%, conversion rate below 0.5%)
   - Hold (within tier performance band)

5. Recommend specific bid adjustments per keyword, with rationale.

6. Recommend any keywords to move between match types
   (broad to phrase, phrase to exact, etc.).

7. Recommend the next 30-day budget allocation across tiers
   based on the recommended adjustments.

8. Identify keywords that should be embedded in the listing
   (title, bullets, backend search terms) based on tier and performance.

Return a structured report with per-keyword recommendations and a
summary tier allocation.
```

The agent pulls PPC data, classifies keywords, recommends adjustments, and produces a 30-day action plan. The whole exercise takes 10 minutes.

---

## What It Does Well

- **Time.** A per-keyword bid review across 500 keywords takes half a day in spreadsheets. One prompt takes 5 minutes.

- **Reproducible.** The same model runs monthly. The seller gets a consistent monthly optimization cycle.

- **Open source.** The MCP server is open source on GitHub. The framework is plain English.

- **Where it runs.** Claude Code, Codex, Cursor — the same AI agent environment the seller uses for other Amazon work.

---

## Where It Falls Short

It does not handle Sponsored Brands and Sponsored Display differently from Sponsored Products. The model treats all keyword data the same. Sponsored Brands and Sponsored Display have different conversion dynamics and require separate analysis.

It does not predict keyword seasonality. A keyword converting at 12 percent in November may convert at 4 percent in February. The model uses 30-day rolling data and does not weight for seasonal patterns.

It does not replace human judgment on competitor bidding. Some competitor bidding strategies (aggressive head-to-head, defensive saturation) require business context the prompt does not have.

---

## Try It Yourself

```bash
git clone https://github.com/DannylydST/sorftime-seller-agent
cd sorftime-seller-agent
python3 scripts/install.py
```

Register for a free API key at [open-intl.sorftime.com](https://open-intl.sorftime.com), connect the MCP server to your AI agent, paste the tier allocation prompt with your 30-day PPC data, and review the per-keyword action plan. Decisions are still yours — the model surfaces the optimization opportunities, you decide which to execute.

[sorftime-seller-agent on GitHub](https://github.com/DannylydST/sorftime-seller-agent) | [Free API Key](https://open-intl.sorftime.com)

---

## Sources

[1] Amazon Advertising — Sponsored Products Bid Strategy Documentation
[2] Amazon Advertising Console — Search Term Report and Negative Keyword Strategy
[3] Sorftime Seller Agent GitHub Repository — https://github.com/DannylydST/sorftime-seller-agent

---

## 审核备注

> 以下为内部审核用，发布前删除。

**综合评级**：✅ 可发布
**已自动修复**：竞对名称/中文叙事/第一人称/感叹号 扫描通过
**建议关注**：Amazon PPC 政策时效性，发布前应复核
**来源状态**：基于 Amazon Advertising 官方文档 + sorftime-seller-agent 开源文档

*初稿完成：2026-08-26 | 待审核*
