---
name: comp-analyzer
description: "Automatically research comparable sales and generate ARV estimates for any property. Use when a wholesaler needs comps to make an offer, build a deal package, or validate their numbers. Uses Perplexity MCP to search for recent sales and Firecrawl MCP to scrape listing sites and county records for detailed comp data. Produces a professional comp report with ARV calculation."
---

# Auto-Comp Analyzer Skill

> **Purpose:** Pull comps, calculate ARV, and generate a deal-ready analysis — without spending 30 minutes on Zillow, PropStream, or county records.

---

## PREREQUISITE
- **Perplexity MCP** — for searching recent sales, market data, and property values
- **Firecrawl MCP** — for scraping Zillow, Redfin, Realtor.com, and county assessor sites for detailed comp data

---

## WHAT THIS SKILL DOES

- Researches comparable sales within a specified radius of the subject property
- Pulls comp data from multiple sources (Zillow, Redfin, county records)
- Calculates ARV using $/sqft analysis with adjustments
- Applies the 70% rule to determine maximum allowable offer
- Produces a professional comp report you can share with buyers
- Flags when comp data is thin or unreliable

## WHAT THIS SKILL DOES NOT DO

- Does not access MLS directly (uses public data sources)
- Does not replace a professional appraisal
- Does not guarantee values — estimates based on available public data
- Does not pull off-market sales that aren't in public records yet

---

## INPUTS NEEDED

| Input | Required | Example |
|-------|----------|---------|
| Subject property address | Yes | "4821 Cedar Ln, Memphis, TN 38118" |
| Property details | Yes | "3/2, 1,100 sqft, built 1960" |
| Property condition | Yes | "Needs full rehab" or "Cosmetic updates only" or "Turn-key" |
| Comp radius | Helpful | "0.5 mile" (default: 1 mile) |
| Time window | Helpful | "Last 60 days" (default: 90 days) |
| Purpose | Helpful | "Making an offer," "Building a deal package for buyers," "Validating my ARV" |
| Estimated repairs | Helpful | "$40,000" (if known) |

---

## GOAL

- Primary: Give the wholesaler a defensible ARV they can use to make offers and sell to buyers with confidence
- Secondary: Save 30+ minutes of manual comp research per property

---

## FRAMEWORK: The 4-Pass Comp Process

### Pass 1 — Wide Search (Perplexity)

Cast a wide net to find recent sales in the area.

**Perplexity query:**
```
perplexity_ask: "What [beds]/[baths] single family homes have sold within 1 mile of [address] in the last 90 days? I need specific addresses, sale prices, square footage, sale dates, and property condition (remodeled vs. original). Focus on comparable properties — similar size, similar age, similar construction."
```

**Also ask:**
```
perplexity_ask: "What is the Zillow Zestimate and Redfin estimate for [address]? Also, what is the median home price and average price per square foot in [zip code]?"
```

**Extract from Perplexity results:**
- List of 5-10 potential comps with addresses and prices
- Zillow/Redfin automated estimates for reference
- Area median price and $/sqft baseline

### Pass 2 — Deep Scrape (Firecrawl)

For the top 5-8 comps from Pass 1, scrape detailed data.

**Scrape Zillow/Redfin for each comp:**
```
firecrawl_scrape: "[zillow or redfin URL for comp address]"
Extract: Sale price, sale date, beds, baths, sqft, lot size, year built, property type, condition notes, days on market, price history, photos assessment
```

**Scrape county assessor for each comp:**
```
firecrawl_scrape: "[county assessor URL for comp address]"
Extract: Assessed value, last sale price/date, property details, tax info
```

**If Zillow/Redfin scrape fails:**
- Try Realtor.com or Homes.com as alternatives
- Fall back to county records only
- Note reduced confidence in the report

### Pass 3 — Filter & Adjust

Not every sale is a good comp. Filter ruthlessly.

**Comp Qualification Criteria:**

| Criteria | Ideal | Acceptable | Reject |
|----------|-------|------------|--------|
| Distance | Under 0.5 mi | 0.5-1.0 mi | Over 1 mi (unless rural) |
| Sale date | Under 60 days | 60-90 days | Over 90 days (unless slow market) |
| Bed/bath count | Same | +/- 1 | +/- 2 or more |
| Square footage | Within 10% | Within 20% | Over 20% difference |
| Property type | Same | — | Different (no condos vs SFR) |
| Condition | Renovated (for ARV) | Partially updated | Distressed (use for as-is value only) |
| Sale type | Arm's length | — | Foreclosure, auction, family transfer (flag these) |

**Adjustments:**

| Factor | Adjustment | Direction |
|--------|-----------|-----------|
| Extra bedroom | +$5,000 to $15,000 | Varies by market |
| Extra bathroom | +$3,000 to $10,000 | Varies by market |
| Garage (subject has, comp doesn't) | +$5,000 to $15,000 | + to subject value |
| Larger lot (>20% difference) | +/- $2,000 to $10,000 | Depends on market |
| Pool | +$5,000 to $15,000 | Market dependent |
| Basement (finished) | +$10,000 to $25,000 | If comp doesn't have one |
| Superior location (busy road, railroad, etc.) | +/- $5,000 to $20,000 | Significant factor |
| Age difference (>10 years) | +/- $2,000 to $8,000 | Newer = more value |

### Pass 4 — Calculate ARV

**Method: Adjusted $/sqft**

1. For each qualified comp, calculate adjusted $/sqft:
```
Adjusted Comp Value = Sale Price +/- Adjustments
Adjusted $/sqft = Adjusted Comp Value / Comp Sqft
```

2. If you have 4+ comps, remove the highest and lowest
3. Average the remaining adjusted $/sqft values
4. Multiply by subject property sqft

```
ARV = Average Adjusted $/sqft x Subject Sqft
```

5. Cross-reference with Zillow/Redfin estimates
6. Assign confidence level

**Confidence Levels:**

| Level | Criteria |
|-------|----------|
| HIGH | 4+ comps, all within 0.5 mi and 60 days, tight $/sqft range (under 10% variance) |
| MODERATE | 3-4 comps, within 1 mi and 90 days, some variance in $/sqft |
| LOW | 1-2 comps, or wide radius, or old sales, or high variance |
| VERY LOW | No good comps found — estimated from zip code averages and automated valuations only |

---

## DECISION LOGIC

```
IF subject is in dense urban/suburban area → Tighten radius to 0.5 mi, should find plenty of comps
IF subject is rural → Expand radius to 3-5 mi, accept older sales (180 days)
IF subject is a unique property (large acreage, commercial zoning, etc.) → Flag as hard-to-comp, lower confidence
IF comps are all renovated but subject needs rehab → Calculate BOTH as-is and ARV
IF automated estimates (Zillow/Redfin) differ from comp analysis by more than 15% → Flag discrepancy and investigate
IF market is appreciating rapidly (>8%/yr) → Note that older comps may understate current value
IF market is declining → Note that recent comps may overstate — be conservative
IF no comps found within criteria → Expand search in stages: wider radius → older dates → adjacent zip codes
DEFAULT → 1 mile radius, 90 days, 3-5 best comps
```

---

## CONSTRAINTS

- [ ] Always show the individual comps — never just give a number without supporting data
- [ ] Always note the source for each comp (Zillow, county records, Perplexity search)
- [ ] Always include confidence level — don't present a shaky ARV as gospel
- [ ] Show adjustments transparently — buyers will question your numbers
- [ ] Include automated estimates (Zestimate, Redfin) for reference but don't rely on them alone
- [ ] If comps are thin, say so. A honest "I only found 2 comps" is better than fake confidence
- [ ] Include a disclaimer that this is not an appraisal
- [ ] Calculate MAO using conservative (high) repair estimates

---

## COMPLETE EXAMPLE

### Example Input:
> Run comps for 2847 Poplar Ave, Memphis, TN 38112. 3/2, 1,450 sqft, built 1955. Brick ranch. Needs full rehab — roof, HVAC, kitchen, both baths, flooring, paint. Estimated repairs $50K-$65K. I'm trying to make an offer.

### Example Output:

---

## COMP ANALYSIS: 2847 Poplar Ave, Memphis, TN 38112

**Subject Property:** 3/2 | 1,450 sqft | Built 1955 | Brick Ranch
**Condition:** Full rehab needed
**Search Parameters:** 1 mile radius | Last 90 days | 3/2 SFR | Renovated condition

---

### COMPARABLE SALES

| # | Address | Sale Price | Sqft | $/Sqft | Beds/Bath | Built | Condition | DOM | Date | Distance | Source |
|---|---------|-----------|------|--------|-----------|-------|-----------|-----|------|----------|--------|
| 1 | 2903 Cowden Ave | $172,000 | 1,380 | $125 | 3/2 | 1958 | Fully renovated | 22 | 45 days ago | 0.3 mi | Zillow |
| 2 | 2760 Poplar Ave | $185,000 | 1,520 | $122 | 3/2 | 1952 | Fully renovated | 18 | 30 days ago | 0.2 mi | Zillow |
| 3 | 3015 Central Ave | $168,000 | 1,400 | $120 | 3/2 | 1960 | Renovated (no new roof) | 35 | 60 days ago | 0.4 mi | Redfin |
| 4 | 2880 Autumn Ave | $192,000 | 1,600 | $120 | 3/2 | 1955 | Fully renovated + addition | 14 | 25 days ago | 0.3 mi | County records |
| 5 | 2790 Walnut Grove | $158,000 | 1,350 | $117 | 3/1 | 1957 | Renovated | 28 | 55 days ago | 0.5 mi | Zillow |

### ADJUSTMENTS

| Comp | Raw $/sqft | Adjustments | Adjusted $/sqft |
|------|-----------|-------------|----------------|
| 1 - Cowden | $125 | None — very comparable | $125 |
| 2 - Poplar | $122 | -$2 (70 sqft larger) | $120 |
| 3 - Central | $120 | +$3 (no new roof — subject will have one post-rehab) | $123 |
| 4 - Autumn | $120 | -$5 (has addition, 150 sqft larger, slightly inflated) | $115 |
| 5 - Walnut Grove | $117 | +$5 (only 3/1, subject is 3/2) | $122 |

**Removing outliers:** Drop Comp 4 (highest adjusted) and Comp 5 (lowest raw — also only 3/1)

**Remaining comps average adjusted $/sqft:** ($125 + $120 + $123) / 3 = **$122.67**

### ARV ESTIMATE

```
ARV = $122.67/sqft x 1,450 sqft = $177,871
Rounded: $178,000
```

**Cross-reference:**
- Zillow Zestimate: $105,000 (as-is — not relevant for ARV)
- Redfin Estimate: $112,000 (as-is)
- Median $/sqft in 38112 (renovated): $118-125

**ARV CONFIDENCE: HIGH**
- 5 comps found, 3 used after filtering
- All within 0.5 mi and 60 days
- Tight $/sqft range ($120-$125)
- Consistent with area medians

### ARV: $178,000

---

### DEAL ANALYSIS (70% Rule)

```
ARV:                        $178,000
70% of ARV:                 $124,600
Repairs (high estimate):    -$65,000
Assignment fee:             -$10,000
───────────────────────────────────────
MAX ALLOWABLE OFFER:        $49,600
Round: $50,000

Using low repair estimate ($50K):
MAO:                        $64,600
Round: $65,000
```

**Offer range: $50,000 - $65,000** depending on your confidence in repair costs.

**Recommended offer: $52,000-$55,000** — leaves room for $10K+ fee even with high repair estimate.

---

### MARKET CONTEXT
- 38112 is an active investor market — flippers and buy-and-hold
- Average DOM for renovated homes: 22 days (fast market)
- Rental rate (3/2 renovated): $1,350-$1,500/month
- Year-over-year appreciation: ~5%
- Multiple flips in progress in the immediate area

---

*This comp analysis is based on publicly available data from Zillow, Redfin, and county records. It is not a professional appraisal. Values are estimates and may differ from actual market outcomes. Always verify comps independently before making final offers.*

---

## MCP USAGE GUIDE

### Perplexity Calls (in order)

**1. Initial comp search:**
```
perplexity_ask: "What [beds]/[baths] single family homes have sold within 1 mile of [address] in the last 90 days? Include specific addresses, sale prices, square footage, sale dates, and whether they were renovated or original condition."
```

**2. Automated estimates:**
```
perplexity_ask: "What is the Zillow Zestimate and Redfin estimate for [address]? What is the average price per square foot for renovated [beds]/[baths] homes in [zip code]?"
```

**3. Market context:**
```
perplexity_ask: "What is the real estate market like in [zip code]? Average days on market, year-over-year price change, rental rates for [beds]/[baths], and investor activity level."
```

### Firecrawl Calls (for each comp)

**1. Zillow detail scrape:**
```
firecrawl_scrape: "https://www.zillow.com/homedetails/[address-slug]/"
Extract: sale price, sale date, beds, baths, sqft, lot size, year built, last listed price, price history, days on market
```

**2. County assessor scrape:**
```
firecrawl_scrape: "[county assessor URL with address]"
Extract: assessed value, last recorded sale, owner name, property details
```

**Fallback if Zillow blocks:**
```
firecrawl_scrape: "https://www.redfin.com/[state]/[city]/[address]"
OR
firecrawl_scrape: "https://www.realtor.com/realestateandhomes-detail/[address]"
```

---

## OUTPUT FORMAT

Deliver comp analyses as:
1. **Subject property summary** — address, details, condition, search parameters
2. **Comparable sales table** — all comps with full data and sources
3. **Adjustments table** — transparent adjustment reasoning
4. **ARV calculation** — show the math step by step
5. **Confidence level** — how reliable is this estimate
6. **Deal analysis** — 70% rule, MAO, offer range
7. **Market context** — DOM, appreciation, rental rates
8. **Disclaimer** — not an appraisal

---

## QUALITY CHECKLIST

Before delivering, verify:
- [ ] Are comps from the last 90 days and within 1 mile (or noted exceptions)?
- [ ] Are comps similar in beds/baths/sqft/age to the subject?
- [ ] Are adjustments reasonable and explained?
- [ ] Is every comp sourced (Zillow, Redfin, county, Perplexity)?
- [ ] Is the confidence level honest?
- [ ] Is the MAO calculated with the high repair estimate?
- [ ] Would a cash buyer trust this report enough to make a decision?
- [ ] Is there a disclaimer about estimates vs. actuals?
