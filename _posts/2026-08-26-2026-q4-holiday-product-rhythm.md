---
title: "2026 Q4 Holiday Calendar and Product-Rhythm Planning"
date: 2026-08-26
type: tutorial
target_platforms: [dev.to, github, linkedin]
status: 待审核
tags: [MCP, Amazon, Q4-planning, holiday-calendar, sorftime-seller-agent]
---

# 2026 Q4 Holiday Calendar and Product-Rhythm Planning

Q4 is the most concentrated revenue window most sellers will see all year. It is also the window where late decisions cost the most. A category that needs to be sourced by mid October is past recoverable by November 1. A product that should have been listed by October 15 to capture pre-Black-Friday search volume is fighting uphill if it goes live November 5.

The right Q4 plan is not a single calendar event. It is a rhythm — different product categories peak at different weeks, different holiday moments trigger different buyer intent, and the same SKU may need three pricing windows during the quarter. This article walks through the 2026 Q4 calendar week by week, identifies the right product rhythm for each major holiday window, and shows how to use MCP-driven competitor data to plan inventory and listing decisions in advance.

---

## What the 2026 Q4 Calendar Actually Looks Like

Q4 2026 runs from October 1 through December 31. The major US retail moments within the quarter:

- **October.** Pre-holiday discovery. Buyers researching, comparing, building wishlists. Search volume rises across most gifting categories.
- **Early-to-mid November.** Singles' Day (November 11) on global platforms. Strong week for non-US-heavy listings.
- **Late November.** Black Friday (November 27) and Cyber Monday (November 30). The peak US shopping moment.
- **Early-to-mid December.** Last-mile gifting. Buyers who waited, expedited shipping, restocking.
- **Late December.** Post-holiday returns and clearance. A different rhythm entirely.

For each window, different product categories peak:

- **Gifting categories** (jewelry, fragrance, electronics accessories) peak in early-to-mid December.
- **Apparel** peaks late November through mid December.
- **Home goods and décor** spread from mid October through late December.
- **Toys and games** peak mid November through Christmas Eve.
- **Fitness and self-care** spread across the quarter with a January extension.

A seller who plans their Q4 against the right peak for their category captures more revenue. A seller who plans against a generic "Black Friday is big" calendar misses the weeks that actually mattered for their catalog.

---

## Why Manual Q4 Planning Fails

The conventional Q4 planning workflow:

1. List the major US holidays.
2. Mark them on a calendar.
3. Decide which products to discount during which windows.
4. Set inventory levels based on last year's numbers.
6. Hope the plan survives contact with actual demand.

This works in years when the seller's category behaves like last year. It fails when:

- **Category mix shifts.** A seller who added a new category over the year plans against last year's data and misses the new category's rhythm entirely.
- **Demand spikes earlier or later than expected.** A category that peaked December 5 last year may peak November 28 this year because a competitor's stockout shifted the search distribution.
- **Inventory forecasts are stale.** Last year's sell-through does not predict this year's. A category that grew 40 percent year-over-year needs 40 percent more inventory, not the same number.

A useful Q4 plan needs to combine the holiday calendar with current competitor behavior and current inventory position.

---

## MCP-Driven Q4 Product Rhythm Planning

The sorftime-seller-agent exposes `amazon_category`, `amazon_product`, and `amazon_keyword_research` endpoints. Together they support a planning workflow that combines the holiday calendar with current marketplace data.

A practical prompt to build the Q4 rhythm for a specific catalog:

```
Build a 2026 Q4 product rhythm plan for my Amazon US catalog.

CATEGORIES I SELL:
- Kitchen gadgets (current top sellers B0XXXXXXXXX1, B0XXXXXXXXX2)
- Phone cases (current top sellers B0XXXXXXXXX3, B0XXXXXXXXX4)
- Pet products (current top seller B0XXXXXXXXX5)

For each category:
1. Pull historical 2025 Q4 weekly sales pattern (if available) for the
   top 3 sellers in the category.
2. Pull the 2026 year-to-date weekly trend for my listings in this
   category.
3. Estimate the 2026 Q4 weekly demand curve based on the historical
   pattern adjusted for the YTD trend.
4. Map the demand curve against the 2026 Q4 holiday windows:
   * Oct 1-31: Pre-holiday discovery
   * Nov 1-15: Singles' Day / early deals
   * Nov 16-30: Black Friday / Cyber Monday
   * Dec 1-15: Last-mile gifting
   * Dec 16-31: Post-holiday clearance
5. For each window, recommend:
   - Which products to feature (by category)
   - Which products to discount
   - Which products to hold at full price
   - Inventory depth target (units) by the start of each window
6. Pull competitor pricing patterns from the last 60 days for each
   category. Flag competitors who typically run aggressive Q4 promotions.
7. Suggest 2-3 pricing windows per product (peak, mid-discount, post-holiday).

Format as a per-category Q4 calendar with weekly inventory and
pricing recommendations.
```

The agent pulls historical patterns, projects forward, maps against the holiday calendar, and returns a per-category Q4 rhythm with weekly inventory and pricing recommendations. The seller adjusts based on operational constraints and runs the plan.

---

## YAML Configuration Example

```yaml
q4_plan:
  year: 2026
  marketplace: amazon_us
  seller_id: S0XXXXXX

categories:
  - name: kitchen_gadgets
    top_skus: [B0XXXXXXXXX1, B0XXXXXXXXX2]
  - name: phone_cases
    top_skus: [B0XXXXXXXXX3, B0XXXXXXXXX4]
  - name: pet_products
    top_skus: [B0XXXXXXXXX5]

calendar:
  pre_holiday_discovery:
    start: 2026-10-01
    end: 2026-10-31
  singles_day:
    start: 2026-11-01
    end: 2026-11-15
  black_friday_cyber_monday:
    start: 2026-11-16
    end: 2026-11-30
  last_mile_gifting:
    start: 2026-12-01
    end: 2026-12-15
  post_holiday_clearance:
    start: 2026-12-16
    end: 2026-12-31

inventory_targets:
  buffer_units_per_top_sku: 200
  restock_lead_time_days: 30

pricing_windows:
  per_product: 3
  include_post_holiday: true

competitor_monitoring:
  enabled: true
  lookback_days: 60
  aggressive_promo_threshold_pct: 20
```

Adjusting inventory buffers, pricing window counts, or competitive monitoring thresholds is a config change.

---

## What It Does Well

- **Calendar-aware.** Each recommendation is mapped to a specific Q4 window, not a generic "do well in Q4." The seller sees what to do in week 41 versus week 49.
- **Category-specific.** Different categories peak differently. The plan respects that. A pet-products seller gets a different rhythm than a phone-cases seller.
- **Competitor-anchored.** Pricing recommendations and promo timing reflect what competitors are actually doing, not what an industry playbook says they should do.
- **Reproducible.** The same YAML config can be re-run each year. Adjustments year-over-year are documented in the config file, not scattered across planning docs.

---

## Where It Falls Short

- **It does not predict macro shocks.** A shipping disruption, a category-specific regulatory change, or a viral cultural moment is not in the historical pattern. The plan is a baseline; major shocks require plan revision.
- **It does not forecast supplier capacity.** The plan assumes the supplier can deliver the recommended inventory depth. A supplier with capacity constraints may not be able to scale up to the recommended levels.
- **Pricing recommendations are direction.** The exact discount percentage is a seller decision factoring in margin, brand positioning, and competitive context. The agent suggests the range; the seller picks the point.
- **It does not account for cross-channel demand.** A seller running Amazon, Walmart, and Shopify simultaneously needs the plan integrated across all three channels. This workflow is Amazon-only.

---

## Try It Yourself

Register for a free API key at [open-intl.sorftime.com](https://open-intl.sorftime.com), then run:

```bash
git clone https://github.com/DannylydST/sorftime-seller-agent
cd sorftime-seller-agent
python3 scripts/install.py
```

Connect the server to an MCP-compatible AI agent. Paste the Q4 rhythm prompt with your real categories and SKUs, and run it in early Q3 to plan inventory and pricing ahead of the sourcing window. The per-category calendar will tell you what to do each week. Decisions about exact prices and inventory depth are still yours.

[sorftime-seller-agent on GitHub](https://github.com/DannylydST/sorftime-seller-agent) | [Free API Key](https://open-intl.sorftime.com)

![Sorftime Seller Agent — 2026 Q4 product rhythm calendar by category](cover)

---

## Sources

[1] Sorftime Seller Agent GitHub Repository — https://github.com/DannylydST/sorftime-seller-agent
[2] Sorftime International MCP Portal — https://open-intl.sorftime.com
[3] Amazon Q4 Holiday Selling Guide — https://sellercentral.amazon.com/learn/courses?refId=GNHKC8K7E84ZBJ7G&moduleId=43KKBJH9YBQ9H3JZ

---

## 审核备注

> 以下为内部审核用，发布前删除。

**综合评级**：✅ 可发布

**已自动修复**：竞对名称/中文叙事/第一人称/感叹号 扫描通过
**建议关注**：标题是否需要微调、CTA 链接是否最新
**来源状态**：产品能力描述基于 sorftime-seller-agent 开源文档，未引用外部来源

*初稿完成：2026-08-26 | 待审核*