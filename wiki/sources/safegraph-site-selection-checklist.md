---
type: source
sources: []
updated: 2026-09-20
confidence: medium
tier: Allowed
raw_file: raw/safegraph-site-selection-checklist-2026.md
---

# SafeGraph: Retail site selection checklist (10 steps)

**What it is:** a blog post by Briana Brown for SafeGraph, first published 2021-06-17 and updated 2026-08-12. Tier: **Allowed** (public article, no client data). Two cautions on confidence:

- **Vendor content.** SafeGraph sells foot-traffic and location data, so the article favors data-driven scoring and mobility data. Its examples are anecdotes with no named sources.
- **Extract, not verbatim.** The raw file is a fetched and summarized version. Check figures against the original before using them outside the wiki.

## The method in brief

The article's 10 steps are: (1) map current stores and trade areas, (2) study competitors and complementary businesses, (3) locate the target demographic, (4) enrich with physical site data, (5) check financial and legal fundamentals, (6) analyze current store performance, (7) find lookalike locations with scoring models, (8) match space size and layout, (9) adjust for the vertical, (10) validate performance after opening.

## Points most relevant to Meridian

- **Start from your own stores.** Analyze existing stores (revenue, trade area, overlap, performance clusters) and use the best performers as the template for candidate sites. Meridian has 14 stores of its own data, which is the natural starting point ([[client-brief]]).
- **Grocery is a distinct vertical.** The article says grocery trips are mostly within a few miles of home, parking carries outsized weight, and competitive saturation matters more than in most retail. Its authors warn against using a generic template across formats.
- **Cannibalization.** A new entrant can quickly cannibalize a thin-margin category. This includes a chain's own stores; an example puts two locations nine minutes apart with 22% visitor overlap.
- **Look beyond revenue.** Visits, dwell time, and seasonality separate strong stores from ones inflated by events. Trailing 12-month revenue alone can mislead.
- **Footprint matters.** The article gives a sales-per-square-foot example and warns against accepting the wrong size in a good location. Meridian's store attributes include square footage ([[client-brief]]).
- **Validate after opening.** Compare projected and actual results at 90 days and 12 months.
- **Market context (as stated):** shopping center vacancy 5.9% in Q1 2026, about 5,500 openings against 7,900 closures projected for 2026, e-commerce at 16.9% of retail sales.

## What this changes for the interview

- Ask how Meridian defines a store's trade area and whether it knows where customers travel from. See [[open-questions]] #12.
- Ask whether earlier openings were tracked against forecasts. See [[open-questions]] #13.
- Frames the analysis Meridian's data could support: comparing Pasadena to its best existing stores. See [[assumptions]] A7 and [[pasadena]].
- **Data rule note:** third-party mobility data is public or purchased data, not Meridian customer data, but any Meridian loyalty or transaction data used alongside it stays Restricted.

Related: [[meridian-markets]], [[icsc-new-grocery-formats]].
