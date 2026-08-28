---
title: "Shopee Multi-Region Brand Matrix: Compare 8 Sites From One Prompt"
date: 2026-08-26
type: how-to
target_platforms: [dev.to, github, linkedin]
status: 待审核
tags: [MCP, Shopee, cross-border, multi-region, sorftime-seller-agent]
cover_image: /assets/images/posts/shopee.png
description: "Search for wireless earbuds on Shopee Malaysia. Show me the top 20 results ranked by monthly sales — include price, rating, seller location, and store type."
---

# Shopee Multi-Region Brand Matrix: Compare 8 Sites From One Prompt

If you sell the same brand across Shopee Singapore, Malaysia, Thailand, Indonesia, Vietnam, the Philippines, Taiwan, and Brazil, you already know the pain: each region's storefront has its own pricing, its own review accumulation, its own seller score. Comparing them by hand means eight browser tabs and a spreadsheet that breaks the moment you refresh.

This article describes a workflow that pulls a single SKU's footprint across all eight Shopee regions in one structured table, using one MCP tool, one prompt, and one AI agent.

---

## The Cross-Region Problem

Shopee is unusual among large marketplaces. A single brand often runs parallel storefronts — one per region — because consumer behavior, pricing tolerance, and competitive landscape differ sharply between markets. A SKU priced at SGD 25 in Singapore may run at MYR 35 in Malaysia and IDR 150,000 in Indonesia. The same SKU accumulates reviews independently in each region. Seller scores are calculated regionally, not globally.

For a brand manager sitting in any of those markets, the daily question is: how is this product performing in the other seven? The honest answer is "I don't know, because checking takes an hour". Sellers often default to monitoring only their home region and treating the other seven as best-effort, which leads to missed repricing opportunities and unnoticed listing suppression.

The data is there. Shopee exposes product-level fields per region. What is missing is the synthesis — the side-by-side comparison that surfaces the deltas worth acting on.

---

## The Manual Workflow That Does Not Scale

To compare a single SKU across eight regions manually:

1. Open Shopee Seller Center. Switch to region one. Search for the SKU by name or model number. Note the price, stock, seller score, review count, and rating.
2. Switch to region two. Repeat the search. Note that the product title may be translated, so the SKU identifier must be matched carefully.
3. Repeat for regions three through eight.
4. Open a spreadsheet. Type one row per region with the fields captured. Format the columns for currency normalization.
5. Compute the deltas: which region has the lowest price, which has the highest review accumulation, which has the lowest seller score, which is closest to going out of stock.

For one SKU, this takes 20 to 30 minutes if you already know the SKU mapping per region. For a brand running 50 SKUs across the matrix — a more typical case — it is the better part of a workday, and the output is a snapshot that expires within hours as prices shift.

The bottleneck is the region switching and the SKU identification step. Each region's storefront returns results in a different language and uses different category taxonomy, so a literal name search does not always return the same listing.

---

## One MCP Tool, One Prompt, One Comparison Table

The sorftime-seller-agent MCP server exposes Shopee search tools that accept a `site` parameter covering each Shopee region. The tool that matters for this workflow:

- `shopee_product_search` — returns product-level data for a given region, including price, monthly sales, review count, rating, stock count, shop location, and shop type (preferred, flagship, ordinary).

Setup:

```bash
git clone https://github.com/sorftime-ai/sorftime-seller-agent.git
cd sorftime-seller-agent
python3 scripts/install.py
```

Then connect the server to an MCP-compatible AI agent and run:

```
Build me a cross-region comparison for the brand "AuroraHome" on Shopee.
The SKU I care about is a stainless steel insulated bottle, model AH-S12.
For each of these Shopee regions: SG, MY, TH, ID, VN, PH, TW, BR:

1. Call shopee_product_search with site=<region> and a query that targets
   the AH-S12 listing. If the listing does not appear in the first page,
   try an alternative search term based on the brand name.
2. From the matched listing, capture:
     - listing price in local currency
     - monthly sales volume
     - review count and average rating
     - stock count (if returned)
     - shop type (preferred / flagship / ordinary)
     - shop location (local or cross-border)
3. Convert each price to USD using a fixed reference rate and present both
   the local-currency value and the USD value in the output.
4. Compute and present these derived columns:
     - price_index: this region's USD price divided by the median USD price
       across all eight regions
     - review_leader: yes if this region has the highest review count
     - stock_risk: yes if stock is below a threshold that would suggest
       restocking is needed
     - shop_type_score: 3 for preferred, 2 for flagship, 1 for ordinary

Output a single table with one row per region and the columns above.
Then write a 5-line summary highlighting: the region with the highest
price (repricing opportunity), the region with the lowest review count
(promotion opportunity), the region with the lowest stock (restock
priority), and the region where the shop type is the weakest (eligibility
upgrade priority).
```

The agent calls `shopee_product_search` eight times — once per region — pulls the structured fields, normalizes currency, computes the derived columns, and returns a single comparison table plus a five-line summary.

![Sorftime Seller Agent — Shopee cross-region SKU comparison](cover)

---

## What It Does Well

- **Time.** A cross-region scan that takes 20 to 30 minutes per SKU runs in under a minute through the agent. The savings scale linearly with SKU count.
- **Reproducible.** The same prompt run on Monday and Friday returns the same columns, the same derived metrics, and the same summary structure. Comparing two runs is a matter of reading two tables.
- **Open source.** Every tool call is auditable. The conversion-rate assumption, the SKU matching logic, and the summary heuristics all live in the prompt — not in a vendor's black box.
- **Cross-region in one client.** You do not need eight browser tabs, eight logins, or eight seller centers. The agent handles the region switching under the hood.

---

## Where It Falls Short

It depends on consistent SKU identification across regions. If the brand uses different titles or images per region, the SKU matching step inside the prompt may miss the right listing and pick a competing product. A seller with very different per-region branding should validate the first run before relying on subsequent runs.

It does not surface listing suppression. If a SKU has been suppressed in one region for policy violation, it may still appear in search results but with restricted visibility. The fields returned by `shopee_product_search` do not always flag suppressed listings distinctly. A weekly manual check inside Seller Center is still wise.

It does not replace regional account managers. Each Shopee region has its own seller support infrastructure, its own promotional calendar, and its own live-stream culture. The comparison table tells you where to look. It does not tell you what to do once you look.

It returns a snapshot. Prices and stock shift through the day. For sellers running flash promotions or participating in regional sales events, the prompt should be re-run hourly rather than daily.

---

## Try It Yourself

```bash
git clone https://github.com/sorftime-ai/sorftime-seller-agent.git
cd sorftime-seller-agent
python3 scripts/install.py
```

Register for a free API key at [open-intl.sorftime.com](https://open-intl.sorftime.com), connect the server to your MCP-compatible AI agent, and run the comparison prompt against your own brand and SKU. The decisions — which region to reprice, which to restock, which to prioritize for shop-type upgrade — are still yours.

[sorftime-seller-agent on GitHub](https://github.com/sorftime-ai/sorftime-seller-agent) | [Free API Key](https://open-intl.sorftime.com)

---

## Sources

[1] Sorftime Seller Agent GitHub Repository — https://github.com/sorftime-ai/sorftime-seller-agent
[2] Sorftime International MCP Portal — https://open-intl.sorftime.com
[3] Shopee Seller Education Hub — https://seller.shopee.com.my/edu

---

## 审核备注

> 以下为内部审核用，发布前删除。

**综合评级**：✅ 可发布

**已自动修复**：竞对名称/中文叙事/第一人称/感叹号 扫描通过
**建议关注**：标题是否需要微调、CTA 链接是否最新
**来源状态**：产品能力描述基于 sorftime-seller-agent 开源文档，未引用外部来源

*初稿完成：2026-08-26 | 待审核*
