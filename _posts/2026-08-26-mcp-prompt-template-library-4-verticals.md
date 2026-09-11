---
title: "4 MCP Prompt Libraries by Vertical: Furniture, Apparel, 3C, Beauty"
date: 2026-08-26
type: tutorial
target_platforms: [dev.to, github, linkedin]
status: 待审核
tags: [MCP, prompts, templates, vertical-specific, sorftime-seller-agent]
---

# 4 MCP Prompt Libraries by Vertical: Furniture, Apparel, 3C, Beauty

A generic Amazon seller prompt and a furniture-specific Amazon seller prompt produce different quality of output. The generic prompt asks for "top competitors." The furniture-specific prompt asks for "top competitors in sectional sofas under $1,500, sorted by review velocity and minimum doorway width." The first returns noise. The second returns a shortlist the seller can act on.

Vertical-specific prompts encode the buyer's actual decision criteria. A furniture buyer cares about dimensional fit, shipping damage rate, and assembly complexity. An apparel buyer cares about size consistency, fabric hand, and return rate by size. A 3C buyer cares about compatibility, firmware update cadence, and warranty claim patterns. A beauty buyer cares about ingredient transparency, skin-type compatibility, and review mention of irritation.

The prompt library below is organized by vertical. Each library is a set of MCP prompts the seller copies, customizes with the seller's ASIN or category, and runs against the sorftime-seller-agent MCP server.

![Sorftime Seller Agent — Vertical Prompt Libraries](cover)

---

## Library 1: Furniture

Furniture buyers evaluate on dimensions, materials, assembly, durability, and shipping economics. The decision criteria are physical. The data signals come from product specs, review content, and return rate patterns.

```bash
git clone https://github.com/DannylydST/sorftime-seller-agent
cd sorftime-seller-agent
python3 scripts/install.py
```

### Prompt F1: Competitor Map by Dimensional Fit

```
Map competitors for my furniture ASIN B0XXXXXXXXX (Amazon US).

Context:
- My product: [product type, e.g., 3-seat sectional sofa]
- My assembled dimensions: L x W x H in inches
- My weight: X lbs
- My price: $X
- My shipping: LTL / FedEx Ground / white-glove

Tasks:
1. Find the top 20 competitors in the same Amazon category with assembled
   dimensions within +/- 20 percent of mine on all three axes.

2. For each competitor, report:
   - Price
   - Review count and average rating
   - Weight
   - Shipping method (LTL, parcel, white-glove)
   - Doorway minimum width (if stated in listing or specs)
   - Assembly time (if stated)
   - Top 3 complaints extracted from 1-3 star reviews

3. Flag competitors whose doorway minimum exceeds 32 inches
   (standard US interior door) — these are restricted to buyers
   with wider doorways.

4. Identify the 3 competitors with the lowest shipping damage rate
   based on review mentions of "damaged," "dented," "scratched," or
   "missing parts."

5. Recommend a positioning angle for my listing based on the gap
   between my product's strengths and the competitors' weaknesses.

Return a structured competitor map.
```

### Prompt F2: Material and Durability Cross-Check

```
Audit my furniture ASIN's material claims against review reality.

Context:
- My ASIN: B0XXXXXXXXX
- My listing's material claims: [paste bullet points]

Tasks:
1. Pull all 1-3 star reviews from the last 12 months.

2. Identify reviews that mention material issues: fabric pilling, frame
   creaking, cushion flattening, finish chipping, joint loosening,
   hardware rusting, fabric tearing.

3. For each material issue type, count the number of reviews
   mentioning it. Calculate percentage of all reviews.

4. Compare my listing's claims to the actual review content. Flag
   any claim that contradicts the review signal.

5. Recommend 3 listing edits to align claims with reality.

6. Recommend 3 product improvements (not listing edits) to address
   the top review complaints.

Return a structured audit report.
```

### Prompt F3: Shipping Economics Breakdown

```
Compute the real landed cost of my furniture product.

Inputs:
- My FOB cost: $X
- Carton dimensions and weight
- My Amazon FBA fulfillment fee tier (standard, oversize, extra-large)
- My current return rate: X percent
- Average return shipping cost: $X

Tasks:
1. Calculate per-unit landed cost:
   - Product cost
   - Ocean freight + duty + last-mile to Amazon FC
   - Amazon FBA fulfillment fee (look up by size tier)
   - Amazon storage fee (monthly, based on cube)
   - Estimated return reserve (return rate × return shipping cost)

2. Calculate break-even price at my current FBA fee tier.

3. Identify the size tier my product falls into (standard, oversize,
   extra-large) and whether downgrading packaging is feasible.

4. Estimate the cost savings of switching to LTL vs parcel for the
   inbound leg.

5. Recommend 3 actions to reduce landed cost per unit by 5 percent
   or more.

Return a structured cost breakdown.
```

---

## Library 2: Apparel

Apparel buyers evaluate on size consistency, fabric quality, color accuracy, and fit. Return rates are structurally higher than other categories — typically 15-30 percent — and the size chart accuracy drives the bulk of returns.

### Prompt A1: Size Chart Accuracy Audit

```
Audit size chart accuracy for my apparel ASIN B0XXXXXXXXX (Amazon US).

Inputs:
- My listing size chart: [paste size chart with measurements]
- My category: [e.g., women's dresses, men's t-shirts, kids outerwear]

Tasks:
1. Pull the top 10 competitor ASINs in my category.

2. Compare my size chart against theirs across all sizes. Flag any
   measurement where my chart differs from the category median by
   more than 1 cm or 0.5 inch.

3. Pull 1-2 star reviews mentioning "runs small," "runs large,"
   "size chart wrong," "doesn't fit," "tight," "loose." Count
   occurrences and calculate the percentage.

4. For each size, estimate the proportion of returns attributable
   to size mismatch based on review language.

5. Recommend specific size chart adjustments based on competitor
   median and review signal.

6. Recommend listing copy changes to flag sizing notes that reduce
   returns (e.g., "runs small, size up").

Return a structured audit.
```

### Prompt A2: Fabric and Care Claim Verification

```
Verify my apparel fabric and care claims against review reality.

Inputs:
- My ASIN: B0XXXXXXXXX
- My fabric claims: [e.g., "100% cotton," "machine wash cold"]
- My care instructions: [paste care label content]

Tasks:
1. Pull 2-3 star reviews mentioning "fabric," "material," "shrank,"
   "faded," "pilled," "thin," "sheer," "scratchy," "rough,"
   "itchy," or "soft."

2. Categorize review mentions into: shrinkage, color fade, pilling,
   transparency, hand feel, durability.

3. Calculate the percentage of reviews mentioning each category.

4. Compare my listing claims to the review signal. Flag claims that
   contradict high-frequency complaints.

5. Recommend 3 listing edits to align claims with reality.

6. Recommend 3 supplier specification changes to address the top
   3 complaint categories.

Return a structured report.
```

### Prompt A3: Style and Color Variant Performance

```
Analyze variant performance for my apparel ASIN B0XXXXXXXXX.

Inputs:
- My ASIN with variants (size × color matrix)
- My sales data per variant for the last 90 days

Tasks:
1. For each color variant, calculate:
   - Units sold
   - Return rate
   - Average review rating
   - Stock on hand

2. Identify the top 3 colors by sales and the bottom 3 by sales.

3. For the bottom 3 colors, check review content for color-specific
   complaints ("color is not as shown," "too bright," "too dull,"
   "different shade").

4. Calculate the revenue impact of discontinuing the bottom 3 colors
   and reallocating inventory to the top 3.

5. Recommend the color discontinuation candidates based on a
   combination of low sales and high return rate.

Return a structured variant analysis.
```

---

## Library 3: 3C (Consumer Electronics)

3C buyers evaluate on compatibility, firmware support, build quality, and warranty reliability. The decision factors are technical and the data sources are spec sheets, firmware update histories, and warranty claim patterns.

### Prompt C1: Compatibility Map

```
Map the compatibility landscape for my 3C ASIN B0XXXXXXXXX.

Inputs:
- My product: [product type and key spec, e.g., "USB-C hub for MacBook"]
- My device compatibility list: [list of supported devices]

Tasks:
1. Identify the top 10 competitor ASINs in my category.

2. For each competitor, extract their claimed device compatibility.

3. Build a matrix showing which devices are supported by each competitor.

4. Identify the devices supported by 8 or more competitors but not by me.

5. Identify the devices supported only by me (differentiation).

6. Pull reviews from the last 12 months on my ASIN mentioning
   "compatibility," "doesn't work with," "not compatible," "works with."

7. Recommend 3 compatibility claims to add based on competitor gaps and
   review signal.

Return a structured compatibility map.
```

### Prompt C2: Firmware Update Cadence Audit

```
Audit firmware and software update patterns for my 3C product category.

Inputs:
- My product type: [e.g., "WiFi 6 router," "smart bulb"]
- My brand: [brand name]
- My competitors' brands: [list]

Tasks:
1. For each brand, search public sources for firmware update history
   in the last 24 months.

2. For each brand, record:
   - Total firmware updates in 24 months
   - Most recent update date
   - Updates that addressed security issues
   - Updates that added new features

3. Compare my update cadence against the top 3 competitors.

4. Pull 1-3 star reviews from my ASIN mentioning "firmware," "update,"
   "bug," "glitchy," "stops working," "needs update."

5. Calculate the percentage of reviews mentioning firmware or update issues.

6. Recommend a firmware update roadmap based on competitor cadence and
   review signal.

Return a structured audit.
```

### Prompt C3: Warranty and Return Pattern Analysis

```
Analyze warranty and return patterns for my 3C ASIN B0XXXXXXXXX.

Inputs:
- My ASIN
- My current warranty: [duration and terms]
- My current return rate: X percent

Tasks:
1. Pull 1-3 star reviews mentioning "warranty," "defective," "broken,"
   "stopped working," "doesn't charge," "won't turn on," "no power."

2. Categorize issues by likely root cause: manufacturing defect,
   shipping damage, user error, design flaw, compatibility issue.

3. Estimate the percentage of returns attributable to each root cause.

4. Pull the top 3 competitors' warranty terms from their listings.

5. Calculate the cost of my current warranty claims as a percentage
   of revenue.

6. Recommend 3 actions to reduce return rate by:
   - Improving product or packaging (manufacturing defect reduction)
   - Improving listing clarity (user error reduction)
   - Improving compatibility documentation (compatibility issue reduction)

Return a structured analysis.
```

---

## Library 4: Beauty

Beauty buyers evaluate on ingredient transparency, skin-type compatibility, and irritation signals. Review content is heavily skewed toward individual experience — what worked for one skin type may not work for another. The data signal is in the texture and pattern of reviews, not the rating average.

### Prompt B1: Ingredient Claim Audit

```
Audit my beauty product's ingredient claims against review reality.

Inputs:
- My ASIN: B0XXXXXXXXX
- My ingredient list (INCI): [paste from listing]
- My key marketing claims: [e.g., "fragrance-free," "for sensitive skin," "non-comedogenic"]

Tasks:
1. Pull 1-3 star reviews mentioning "irritation," "burning," "redness,"
   "rash," "breakout," "allergic," "reaction," "stinging," "itching,"
   "sensitive."

2. Calculate the percentage of reviews mentioning irritation or reaction.

3. Compare my ingredient list against reviews mentioning specific
   ingredients (e.g., "the retinol broke me out," "the fragrance is strong").

4. Identify the top 3 ingredients most associated with negative reviews.

5. Compare my claims to the review signal. Flag any claim contradicted
   by 5 percent or more of reviews.

6. Recommend 3 listing edits to align claims with reviewer experience.

7. Recommend 3 formulation changes to address the top irritation
   signals.

Return a structured audit.
```

### Prompt B2: Skin-Type Compatibility Map

```
Map skin-type compatibility for my beauty ASIN B0XXXXXXXXX.

Inputs:
- My product: [e.g., "moisturizer for dry skin"]
- My claimed skin types: [e.g., "dry," "normal," "combination"]

Tasks:
1. Pull reviews from the last 12 months.

2. For each review, extract the reviewer's stated or implied skin type
   (look for "my skin is," "I have oily skin," "sensitive skin,"
   "acne-prone," etc.).

3. Categorize reviews by skin type.

4. For each skin type, calculate:
   - Number of reviews
   - Average rating
   - Top complaint category

5. Identify the skin types where my product performs well (avg rating 4+).

6. Identify the skin types where my product performs poorly (avg rating
   below 3.5 or complaint rate above 20 percent).

7. Recommend:
   - Skin types to add to my claimed compatibility list
   - Skin types to remove or flag with a "not recommended for" note
   - Listing copy adjustments by skin type

Return a structured compatibility map.
```

### Prompt B3: Competitor Ingredient Comparison

```
Compare ingredient profiles across top competitors in my beauty category.

Inputs:
- My ASIN: B0XXXXXXXXX
- My product: [product type]
- Top 10 competitors in my category

Tasks:
1. Extract the ingredient list (INCI order) for each competitor.

2. Build a comparison matrix:
   - Active ingredients (and their position in the INCI list)
   - Preservative system
   - Fragrance presence
   - Notable ingredients (retinol, hyaluronic acid, niacinamide, etc.)

3. Identify ingredients present in 7 or more competitors but absent
   from my formula.

4. Identify ingredients in my formula that are absent from 7 or more
   competitors (differentiation).

5. Pull review signals for each ingredient category (positive or negative).

6. Recommend 3 formulation adjustments based on competitor analysis
   and review signal:
   - Ingredients to add
   - Ingredients to remove
   - Ingredient position changes (e.g., moving an active earlier in
     the INCI list to increase concentration)

Return a structured comparison.
```

---

## What the Libraries Have in Common

Each library follows the same structure:

1. **Context block.** The seller provides the ASIN, category, and any specific inputs (listing copy, size chart, ingredient list).

2. **Task list.** A numbered set of analytical tasks the agent executes.

3. **Output specification.** A defined structure for the report — competitor map, audit report, analysis, comparison.

The structure is reusable. The seller copies the prompt, replaces the context block with their own data, and runs the prompt against the same MCP server. The output format stays consistent across runs, which makes month-over-month comparison possible.

The libraries are also stackable. A seller running the Furniture prompts F1, F2, and F3 in sequence gets a complete competitor map, material audit, and shipping economics breakdown for one product. The total time is three prompts plus the time to read the outputs.

---

## What It Does Well

- **Time.** A vertical-specific audit that takes a category expert half a day takes one prompt per audit. Three audits for one product is three prompts in 15 minutes.

- **Reproducible.** The same library runs for every product in the vertical. The seller builds institutional knowledge in prompt form, not in analyst heads.

- **Open source.** The MCP server is open source on GitHub. The prompts are plain English. The libraries are open to modification.

- **Where it runs.** Claude Code, Codex, Cursor — the same AI agent environment the seller uses for general Amazon work.

---

## Where It Falls Short

It does not replace category expertise. The libraries encode common analytical patterns. A seller with deep domain expertise will catch nuances the prompts miss. Use the libraries as a starting point, not the final word.

It does not handle multi-SKU portfolio analysis well. The prompts are written for single-ASIN analysis. A seller with 200 SKUs needs a portfolio-level layer above these prompts.

It depends on review content quality. For ASINs with low review volume, the prompt's review-based signals are statistically thin. The libraries work best for ASINs with at least 100 reviews.

---

## Try It Yourself

```bash
git clone https://github.com/DannylydST/sorftime-seller-agent
cd sorftime-seller-agent
python3 scripts/install.py
```

Register for a free API key at [open-intl.sorftime.com](https://open-intl.sorftime.com), connect the MCP server to your AI agent, and pick the library for your vertical. Copy a prompt, replace the context block with your own ASIN and category, and run. Decisions are still yours — the analysis surfaces the signals, you decide what to change.

[sorftime-seller-agent on GitHub](https://github.com/DannylydST/sorftime-seller-agent) | [Free API Key](https://open-intl.sorftime.com)

---

## Sources

[1] Sorftime Seller Agent GitHub Repository — Prompt Library Documentation
[2] Amazon Seller Central — Category-Specific Listing Best Practices
[3] MCP Protocol Specification — Tool and Prompt Conventions

---

## 审核备注

> 以下为内部审核用，发布前删除。

**综合评级**：✅ 可发布
**已自动修复**：竞对名称/中文叙事/第一人称/感叹号 扫描通过
**建议关注**：各平台规则时效性，建议发布前复核
**来源状态**：基于 sorftime-seller-agent 开源文档 + 各平台官方文档

*初稿完成：2026-08-26 | 待审核*
