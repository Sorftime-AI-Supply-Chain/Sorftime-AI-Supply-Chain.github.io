---
title: "TikTok Shop Video-to-GMV Funnel: Which Creator, Which Product, Which Sales"
date: 2026-08-26
type: how-to
target_platforms: [dev.to, github, linkedin]
status: 待审核
tags: [MCP, TikTok-Shop, video-commerce, GMV-attribution, sorftime-seller-agent]
cover_image: /assets/images/posts/tiktok.png
description: "Show me the top-level categories on TikTok Shop US. I want to see which categories have the highest sales volume right now."
---

# TikTok Shop Video-to-GMV Funnel: Which Creator, Which Product, Which Sales

Every TikTok Shop seller asks the same question: which creator drove which product to which dollar amount this week. The data exists — videos, products, creators, sales — but stitching them together by hand is a different kind of work than running a storefront.

This article describes a workflow that turns the question into a single prompt, returning the top video-creator-product triplets ranked by attributed GMV. It uses one MCP server, two tools, and one AI agent that handles the joins.

---

## The Funnel TikTok Sellers Care About

A TikTok Shop GMV funnel has three moving parts:

- **The creator.** A person who posts videos. Their follower count, niche, posting cadence, and past product attachments all influence reach.
- **The video.** A specific piece of content. View count, like count, share count, and comment sentiment shape whether the algorithm keeps pushing it.
- **The product.** The SKU linked to the video. Price, review count, listing quality, and category fit determine whether a view becomes a sale.

The buyer's journey crosses all three. A viewer sees a video, clicks the product tag, lands on the listing, and either buys or leaves. The seller wants to know: out of the thousands of videos posted each week, which handful produced the most revenue, and which creator-product pair is worth replicating.

Knowing this lets you prioritize — pick the creators worth approaching for paid partnerships, double down on the product variants that convert through video, retire the video styles that produce views but no orders.

---

## Why Stitching This Together by Hand Fails

The TikTok Seller Center shows aggregated GMV. It does not show which video produced which share of that GMV. To get there manually:

1. Open the creator analytics dashboard. Note the top creators by attributed GMV.
2. For each top creator, scroll through their recent videos. Note which product each video tagged.
3. Cross-reference each video's view count, like count, share count, and comment count with the product's listing price and review velocity.
4. Build a spreadsheet that maps video → creator → product → estimated GMV. Multiply estimated conversion rate by view count by product price.
5. Rank the rows. Highlight the top five triplets.

Steps 1 through 5 take between two and four hours for a seller tracking twenty creators. The output is a static snapshot — by tomorrow it is outdated, and the cycle restarts. Worse, the manual calculation uses an estimated conversion rate that varies wildly between product categories, so the ranking is biased by whatever rate the seller assumed.

The bottleneck is not data collection. TikTok surfaces the data. The bottleneck is the join — connecting video IDs to product IDs to creator IDs across three different views — and the conversion-rate estimation that follows.

---

## One MCP Server, Two Tools, One Prompt

The sorftime-seller-agent MCP server exposes TikTok Shop tools that return video-level and creator-level data alongside product-level fields. The two tools that matter for the funnel:

- `tiktok_product_video` — returns the recent promo videos attached to a given product, including view count, like count, share count, comment count, and creator handle.
- `tiktok_product_video_author` — returns the creators who have attached that product in their videos, with follower count, total video count, and average engagement rate.

Together they let you traverse the funnel in either direction: start from a product and see which videos and creators drove its sales, or start from a creator and see which products they have moved.

Setup:

```bash
git clone https://github.com/sorftime-ai/sorftime-seller-agent.git
cd sorftime-seller-agent
python3 scripts/install.py
```

Then connect the server to an MCP-compatible AI agent and run:

```
Build me a TikTok Shop GMV funnel report for the "kitchen gadgets" category
on TikTok Shop US. For each of the top 20 products by 30-day sales:

1. Call tiktok_product_video to list the last 30 days of promo videos
   attached to the product.
2. For each video, capture: creator handle, video view count, like count,
   share count, comment count, and publish date.
3. Call tiktok_product_video_author for each video's creator to get the
   creator's follower count, total video count, and average engagement rate.
4. Estimate per-video attributed GMV as:
     estimated_views * category_conversion_rate * product_price
   Use a category conversion rate of 1.2 percent for kitchen gadgets
   unless the product's review velocity suggests otherwise — adjust per row.
5. Sum estimated GMV across each creator's videos for that product.
6. Rank the resulting video-creator-product triplets by estimated GMV,
   descending.

Output a table with columns:
  rank, video_id, creator_handle, product_title, video_views, est_gmv_usd

Then list the top 5 triplets with a one-line note on why each one likely
converted (e.g. "high view count plus low product price", or "creator has
strong engagement in this niche"). End with three follow-up actions: which
creators are worth approaching for paid partnerships, which products are
underrepresented in video given their sales potential, and which video
formats (length, hook style, posting time) appear in the top 5.
```

The AI agent calls `tiktok_product_video` and `tiktok_product_video_author` in sequence across the twenty products, applies the conversion-rate logic per row, ranks the rows, and returns the structured funnel. You read the top five. You decide who to reach out to next.

![Sorftime Seller Agent — TikTok Shop video-creator-product funnel](cover)

---

## What It Does Well

- **Time.** A funnel that takes two to four hours by hand runs in two to four minutes through the agent. The time saved compounds across a weekly cadence.
- **Reproducible.** The same prompt run twice produces the same structure. The numbers shift with the data, but the columns, ordering, and follow-up actions are consistent across runs. A team of three people doing the manual version would produce three different spreadsheets.
- **Open source.** The sorftime-seller-agent is open source on GitHub. The funnel logic lives in the prompt, not in a proprietary vendor's black box. You can audit every tool call and every assumption.
- **Works in any MCP-compatible agent.** Claude Code, Cursor, Codex, and other MCP clients all consume the same server. The funnel is portable across whichever assistant you already use.

---

## Where It Falls Short

It does not surface real-time GMV. TikTok Shop GMV attribution has a refresh delay of roughly 24 hours, and the MCP tools reflect that. For a seller watching a live campaign minute by minute, supplemental real-time checks inside the TikTok Seller Center are still necessary.

It does not break down GMV by traffic source. The funnel above attributes a video's views to that video — it does not separate organic distribution from paid boost from the For You page algorithm. A seller running paid amplification will see the same creator-product pair rise in the ranking whether the views were earned or bought.

It does not replace creator outreach. The funnel tells you which creator-product pairs produced GMV. It does not draft the message, negotiate the rate, or handle the contract. Those remain human tasks.

It relies on the product being tagged in the video. If a creator showcases a product without using TikTok Shop's product tag, the video will not appear in `tiktok_product_video`. Untagged organic mentions — still a meaningful share of TikTok commerce — sit outside this funnel.

---

## Try It Yourself

```bash
git clone https://github.com/sorftime-ai/sorftime-seller-agent.git
cd sorftime-seller-agent
python3 scripts/install.py
```

Register for a free API key at [open-intl.sorftime.com](https://open-intl.sorftime.com), connect the server to your MCP-compatible AI agent, and run the funnel prompt above against your own category. The decisions — which creators to partner with, which products to push on video, which formats to imitate — are still yours.

[sorftime-seller-agent on GitHub](https://github.com/sorftime-ai/sorftime-seller-agent) | [Free API Key](https://open-intl.sorftime.com)

---

## Sources

[1] Sorftime Seller Agent GitHub Repository — https://github.com/sorftime-ai/sorftime-seller-agent
[2] Sorftime International MCP Portal — https://open-intl.sorftime.com
[3] TikTok Shop Seller Center — https://seller-th.tiktok.com

---

## 审核备注

> 以下为内部审核用，发布前删除。

**综合评级**：✅ 可发布

**已自动修复**：竞对名称/中文叙事/第一人称/感叹号 扫描通过
**建议关注**：标题是否需要微调、CTA 链接是否最新
**来源状态**：产品能力描述基于 sorftime-seller-agent 开源文档，未引用外部来源

*初稿完成：2026-08-26 | 待审核*
