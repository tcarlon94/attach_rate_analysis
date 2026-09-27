# Merchant Attach Rate & Revenue Analysis

## Overview

This portfolio case study uses synthetic merchant, order, product, and warranty-contract data to evaluate attachment performance and identify a business-development opportunity for a product-protection platform.

I validated data across four related tables, resolved inconsistencies that could bias the core metrics, calculated unit and dollar attach rates by month, merchant, and industry, estimated platform net revenue, and translated the findings into an executive recommendation.

> **Portfolio disclosure:** This project uses synthetic assessment data and does not contain production, customer, or confidential company data.

## Business Questions

- What are the overall unit and dollar attach rates?
- How does attachment change by month and merchant?
- Which data-quality issues could distort the results?
- Which merchant industry combines strong product fit, favorable economics, and meaningful market opportunity?
- What additional data and experiments would help improve attachment?

## Executive Recommendation

**Prioritize Sports & Fitness Equipment in business development.**

The industry produced the strongest combination of warranty adoption, platform economics, and addressable-market potential:

| Metric | Sports & Fitness Equipment |
|---|---:|
| Unit attach rate | **9.57%** |
| Dollar attach rate | **1.52%** |
| Warranty sales | **$91.4K** |
| Estimated platform net revenue | **$65.1K** |
| Share of total net revenue | **49.6%** |

Sports & Fitness generated more than three times the net revenue of any other industry in the dataset. A roughly $58 billion U.S. sporting-goods retail market also provides a large directional market-size proxy for expansion.

## Data Model

| Dataset | Grain | Analytical Role |
|---|---|---|
| Orders | One row per order | Purchase date, channel, price, geography, and merchant |
| Order lines | One row per product line | Quantity, product price, and warrantable status |
| Contracts | One row per protection contract | Warranty sale, refund status, plan term, and protected line item |
| Merchants | One row per merchant record | Merchant name, industry, approval status, and revenue share |

The analysis connected orders to order lines through `order_id`, contracts to products through `line_item_id`, and transactional records to merchant attributes through standardized store identifiers.

## Metric Definitions

### Unit Attach Rate

```text
non-refunded contracts linked to warrantable products
-----------------------------------------------------
             warrantable product units
```

### Dollar Attach Rate

```text
       non-refunded warranty sales
-----------------------------------------
       warrantable product sales
```

### Platform Net Revenue

```text
warranty sales × (1 − merchant revenue share)
```

These definitions keep the numerator and denominator at compatible grains and prevent refunded contracts or non-warrantable products from inflating performance.

## Key Findings

### Overall Performance

| Metric | Result |
|---|---:|
| Overall unit attach rate | **7.18%** |
| Overall dollar attach rate | **0.93%** |

### Monthly Trend

| Month | Unit Attach Rate | Dollar Attach Rate |
|---|---:|---:|
| March 2020 | 9.09% | 1.34% |
| April 2020 | 8.22% | 0.97% |
| May 2020 | 6.34% | 0.84% |

Both attachment measures declined during the three-month period. Unit attachment fell by 2.75 percentage points, while dollar attachment fell by 0.50 percentage points. This warrants deeper investigation into whether the decline came from changes in merchant and product mix or weakening performance within existing merchants.

### Merchant Performance

- ElectricSkatePark had the highest overall unit attach rate at **17.68%**.
- ElectricSkatePark also led dollar attachment at **2.81%**.
- High attach rate did not always translate into the largest revenue opportunity because merchants differed substantially in sales volume and product value.
- Several high-volume merchants had comparatively low attachment, creating potential optimization opportunities within the existing portfolio.

## Data-Quality Decisions

The analysis identified and addressed several issues that materially affected metric reliability:

- **Inconsistent acquisition sources:** Some `source_name` values contained numeric application IDs. Values with clear mappings were standardized; ambiguous values were labeled unknown rather than imputed without evidence.
- **Missing warrantable status:** Approximately 7,000 order-line records had no `is_warrantable` value. These were retained but excluded from attach-rate denominators because the data could not establish whether they were eligible.
- **Contract and quantity mismatches:** Some line items contained more contracts than purchased product units. Excess exact duplicates were removed while legitimate multi-unit protection contracts were retained.
- **Conflicting eligibility:** Some products had protection contracts even though their order-line record was marked non-warrantable. Contracts and order lines were joined at `line_item_id` so only internally consistent records contributed to the final unit metric.
- **Merchant-status discrepancies:** Transaction activity existed for a merchant identifier marked unapproved. Transactional identifiers were treated as the source of truth for performance analysis rather than excluding valid sales.
- **Extreme order values:** Large purchases were traced to a luxury-watch merchant and retained because they were plausible for that business rather than automatically treated as errors.

These choices favor transparent exclusions and traceable assumptions over unsupported imputations.

## Analytical Workflow

1. Loaded and profiled four relational datasets with Pandas.
2. Standardized dates, merchant identifiers, geography, categories, and missing values.
3. Investigated duplicates, extreme values, null warrantability, and cross-table conflicts.
4. Reconciled product quantities with protection contracts at the line-item grain.
5. Calculated unit and dollar attachment overall, monthly, and by merchant.
6. Aggregated performance by industry and calculated net revenue after merchant share.
7. Combined observed economics with directional market sizing.
8. Presented an executive recommendation and a roadmap for further analysis.

## Recommended Next Steps

### Diagnose the Attachment Decline

Decompose the March-to-May decline into:

- Changes in merchant, product, and customer mix
- Performance deterioration within existing merchants
- Warranty-to-product price ratio
- Offer placement and offer impressions
- Device and acquisition channel
- Merchant onboarding cohort

This analysis could identify the highest-impact segments and produce hypotheses for controlled checkout experiments.

### Explain Within-Industry Variation

Unit attachment varied from **3.7% to 15.1%** within Consumer Electronics and from **6.5% to 17.7%** within Sports & Fitness. Product mix, pricing, placement, device, and customer traffic could explain why merchants in the same industry performed differently.

A segmented regression analysis could quantify the strongest drivers, followed by targeted A/B tests to determine whether successful strategies can be replicated at underperforming merchants.

## Limitations

- The dataset is synthetic and covers only three months.
- Missing warrantability may slightly overstate attachment if some excluded records were actually eligible.
- Market size is a directional proxy and may not align exactly with the merchant taxonomy in the transactional data.
- The analysis observes relationships but does not establish causation.
- Offer impressions, checkout placement, customer characteristics, product attributes, and experiment assignments were unavailable.
- Revenue analysis does not include acquisition cost, servicing cost, claims expense, or lifetime value.

## Repository Contents

| File | Description |
|---|---|
| [`attach_rate_analysis.ipynb`](attach_rate_analysis.ipynb) | Data validation, relational analysis, attach-rate calculations, industry evaluation, and recommendations |
| [`attach_analysis_slides.pptx`](attach_analysis_slides.pptx) | Executive presentation summarizing the opportunity and recommended next steps |

The source datasets are intentionally excluded. The notebook retains its analytical outputs but requires the original local files to rerun.

## Tools

Python, Pandas, NumPy, Matplotlib, Seaborn, relational joins, data validation, KPI development, segmentation, revenue analysis, and executive communication.
