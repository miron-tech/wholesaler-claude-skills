---
name: property-recon
description: "Pull a full property intel report from a single address — owner info, tax data, estimated value, recent comps, neighborhood analysis, and liens. Use when a wholesaler needs to research a property before making contact or an offer. Uses Firecrawl for Redfin/Zillow scraping, Chrome extension for county assessor/trustee sites, and Perplexity for market data and comps."
---

# Property Recon Skill

> **Purpose:** Give a wholesaler everything they need to know about a property before they ever pick up the phone — owner, value, taxes, comps, liens, neighborhood — all from one address.

---

## PREREQUISITE
- **Perplexity MCP** — market data, comps, neighborhood intel
- **Firecrawl MCP** — scraping Redfin/Zillow property pages
- **Claude in Chrome MCP** — navigating county assessor/trustee/recorder sites (interactive forms that Firecrawl cannot handle)

> **Why Chrome?** County government sites (assessors, tax collectors, recorders) use JavaScript-heavy interactive forms. Firecrawl fails on these consistently. The Chrome extension fills forms, clicks buttons, and reads results — solving the #1 data gap in property recon.

---

## WHAT THIS SKILL DOES

- Takes a single property address and builds a complete intel report
- Pulls property details (beds, baths, sqft, year built, lot size) from Redfin/Zillow
- Identifies the owner name and mailing address from county assessor (via Chrome)
- Pulls tax payment status and delinquency history from county trustee (via Chrome)
- Estimates property value using recent comparable sales
- Checks for liens, code violations, and transfer history
- Analyzes the neighborhood (median values, DOM, rental rates, trends)
- Flags motivation signals (tax delinquent, long ownership, out-of-state owner, vacant)

## WHAT THIS SKILL DOES NOT DO

- Does not skip trace (no phone numbers or emails — that requires paid data)
- Does not guarantee data accuracy — public records can be outdated
- Does not replace a title search or professional appraisal
- Does not access MLS data directly

---

## INPUTS NEEDED

| Input | Required | Example |
|-------|----------|---------|
| Property address | Yes | "4973 Cedar View Rd, Memphis, TN 38118" |
| What you already know | Helpful | "Drove by it, looks vacant, overgrown yard" |
| Purpose | Helpful | "Deciding whether to send an offer" or "Building a comp report for my buyer" |

---

## GOAL

- Primary: Arm the wholesaler with enough data to make a confident first contact or offer decision
- Secondary: Identify motivation signals that indicate the owner may be ready to sell

---

## FRAMEWORK: The 7-Layer Recon Process

> **Execution order matters.** Layers 1, 3, and 6 can run in parallel. Layer 2 depends on Layer 1 output. Layer 4 uses the parcel number from Layer 1 as a bridge. Layer 7 compiles everything.

### Layer 1 — Property Details & Parcel Number (Firecrawl → Redfin)

**Action:** Scrape the Redfin listing page for the property. This is the fastest, most reliable source for basic property data AND gives you the parcel number you'll use for county lookups.

**How to find the Redfin URL:**
- Pattern: `https://www.redfin.com/[STATE]/[CITY]/[ADDRESS-HYPHENATED]-[ZIP]/home/[ID]`
- If you don't know the Redfin URL, use Firecrawl search: `"[address]" site:redfin.com`
- Or use Perplexity: "[address] redfin listing"

**Use Firecrawl to scrape Redfin and extract:**
```
firecrawl_scrape: [redfin URL]
Format: JSON
Extract: address, bedrooms, bathrooms, sqft, year_built, lot_size, property_type,
         redfin_estimate, last_sale_date, last_sale_price, assessed_value,
         annual_tax, construction, heating, cooling, parcel_number,
         price_history, tax_history
```

**Key data from this layer:**
- Beds, baths, sqft, year built, lot size, construction type
- Redfin estimate (and/or Zillow Zestimate)
- Last sale date and price
- Tax assessed value and annual tax amount
- **Parcel number** — CRITICAL, use this for all county lookups in later layers
- Tax history (multi-year) and price history

**Use `proxy: "stealth"` on Redfin scrapes** — they block basic proxies.

**If Redfin fails:** Try Zillow. Find the zpid via Firecrawl search `"[address]" site:zillow.com`, then scrape. Zillow URLs are less predictable — always search first, don't guess the zpid.

### Layer 1.5 — PropStream Deep Intel (Chrome → PropStream) [OPTIONAL]

**Action:** If the user has PropStream open in Chrome, search the property to pull foreclosure status, liens, mortgage data, condition scoring, and comps that are NOT available from free sources.

**Detection logic:**
```
Check Chrome tabs for any tab with "propstream" in the URL.
IF PropStream tab exists AND user is logged in →
  Run Layer 1.5 (automatic — no prompt needed)
IF PropStream tab does NOT exist →
  Skip Layer 1.5
  At end of report, show PROPSTREAM GAP SUMMARY (see below)
```

**Step-by-step Chrome workflow (if PropStream is available):**

1. **Find the PropStream tab** — check tabs_context for URL containing "propstream"
2. **Navigate** to PropStream search if not already there: `app.propstream.com/search`
3. **Type the address** in the search bar
4. **Wait** for autocomplete suggestion, click it
5. **Wait** 4 seconds for property card to load
6. **Click** the property detail link (href containing `/search/[property-id]`)
7. **Wait** 4 seconds for detail page to load
8. **Screenshot** the detail page header (Value, Mortgage & Debt, Status)
9. **Scroll down** to PropStream Intelligence section (condition scoring)
10. **Screenshot** condition scores
11. **Click** "Comparables & Nearby Listings" tab
12. **Screenshot** comp table

**Extract from PropStream:**
- **Estimated Value** — PropStream's own AVM (compare to Redfin + county)
- **Distressed status** — Bank Owned, Pre-Foreclosure, Short Sale, or None
- **Document type** — Trustee's Deed = foreclosure, Warranty Deed = normal sale
- **Sale amount** — actual or estimated purchase price at last transfer
- **Purchase method** — Cash vs Financed
- **Open mortgages** — count and estimated balance
- **Involuntary liens** — count and dollar amount
- **Estimated equity** — PropStream's equity calculation
- **Owner type** — Trust, Individual, Corporate, etc.
- **Owner status** — Owner Occupied vs Non-Owner Occupied
- **Occupancy** — Occupied vs Vacant
- **Length of ownership** — months/years since last transfer
- **Condition scoring** — Total, Exterior, Interior, Kitchen, Bathroom (Poor/Average/Good)
- **Avg comps** — PropStream's comp average
- **Est. rent** — PropStream's rental estimate
- **Nearby counts** — Pre-foreclosures, Bank Owned, Cash Buyers, Flip Comps

**PropStream condition scores affect rehab estimates:**
```
IF Total Condition = "Poor" →
  Increase rehab estimate range by 30-50%
  Flag: "PropStream rates condition as POOR — expect higher rehab costs"

IF Exterior = "Poor" AND Interior = "Average" →
  Focus rehab on curb appeal, roof, siding, landscaping
  Interior may need only cosmetic updates

IF Kitchen = "Poor" →
  Add $8K-$15K for kitchen renovation to rehab estimate
```

**PropStream foreclosure data changes the deal analysis:**
```
IF Document Type = "Trustee's Deed" →
  Flag: FORECLOSURE PURCHASE
  Note the purchase price — owner likely got a steep discount
  Adjust negotiation strategy: owner has margin, but may want quick profit
  Check if sale price < 50% of estimated value → likely auction deal

IF Distressed = "Bank Owned" →
  Current owner bought from bank/auction
  They are an investor, not a distressed homeowner
  Negotiation is investor-to-investor, not motivated seller outreach
```

**PROPSTREAM GAP SUMMARY (show when PropStream is NOT available):**

Display this at the end of the report, after FILES SAVED and before WHAT'S NEXT:

```
  ──────────────────────────────────────────────

  DATA GAPS — PropStream Would Add

  ○ Foreclosure status     Was this a trustee sale?
  ○ Lien confirmation      Liens verified or ruled out
  ○ Mortgage balance       Open mortgages + est. balance
  ○ Condition scoring      AI-rated exterior/interior/kitchen
  ○ Comp averaging         Auto-calculated from nearby sales
  ○ Skip trace             Owner contact info

  These gaps affect motivation score accuracy
  and rehab estimate confidence.

  → 7-day free trial: trial.propstreampro.com/thrivemode
```

> **Important:** Never pressure. The free recon is valuable on its own. PropStream just fills gaps that would otherwise require manual research. Show the gaps honestly and let the user decide.

---

### Layer 2 — Owner & Assessor Data (Chrome → County Assessor)

**Action:** Use the Chrome extension to navigate the county assessor website, search by address, and extract owner information that is NOT available on Redfin/Zillow.

**Step-by-step Chrome workflow:**

1. **Find the assessor URL** — Use Perplexity: "[County] [State] property assessor search by address"
2. **Navigate:** `chrome_navigate → [assessor URL]`
3. **Screenshot** to see the form layout
4. **Find** the search fields (street number, street name)
5. **Fill the form:**
   - Street number field → enter the house number only
   - Street name field → enter street name WITHOUT direction or suffix (e.g., "Cedar View" not "Cedar View Rd")
6. **Click Submit** and wait 3 seconds for results
7. **Screenshot** the results page
8. **Scroll and screenshot** to capture all sections:
   - Property Location and Owner Information
   - Appraisal and Assessment Information (click to expand)
   - Improvement Details (click to expand)
   - Sales History (click to expand)

**Extract from assessor:**
- **Owner name** (individual or entity — LLC, Trust, Corp)
- **Owner mailing address** (compare to property address → absentee check)
- **Land appraisal vs building appraisal** (separate values)
- **Total appraisal** (county's market value opinion)
- **Total assessment** (taxable value — usually 25% of appraisal in TN)
- **Subdivision name and lot number**
- **Sales history with deed numbers and instrument types**
- **Improvement details:** stories, exterior walls, rooms, beds, baths, basement, heating, fireplace, sqft

**Ownership analysis (from this data):**
```
IF owner name contains "LLC," "Inc," "Trust," "Corp" →
   Flag: ENTITY OWNED
   Action: Search Secretary of State business filings for the entity

IF mailing address ≠ property address →
   Flag: ABSENTEE OWNER
   Note the mailing address city/state — out-of-state absentee = higher motivation

IF mailing address = property address →
   Note: OWNER-OCCUPIED — lower motivation unless other signals present

IF last sale date > 10 years ago →
   Flag: LONG-TERM OWNER (likely high equity)

IF property is listed as "vacant" or "exempt" →
   Flag: POSSIBLE VACANCY
```

**Data conflicts between Redfin and county:**

When Redfin and county assessor disagree on property details (baths, foundation, sqft, etc.), note BOTH values and ask the user which they want to use. Don't silently pick one.

```
Example:
  Redfin says: 2 bathrooms, Pier & Beam + Slab foundation
  County says: 1 bathroom, Slab foundation

  → Show both in the report:
    "Baths: 1 (DCAD) — Redfin lists 2. Which is correct?"
    "Foundation: Slab (DCAD) — Redfin lists Pier & Beam + Slab. Which is correct?"

  → Use county as the default display value (county records are more reliable
    for structural details), but flag the discrepancy so the user can verify.
```

### Layer 3 — Value Estimate (Perplexity)

**Run in parallel with Layer 2.** Use Perplexity to pull comparable sales.

**Prompt Perplexity with:**
> "What have [beds] bedroom single family homes sold for within 1 mile of [address] in the last 6 months? Include specific addresses, sale prices, square footage, sale dates, and price per square foot. Also include the current Zillow Zestimate or Redfin estimate for [address] if available."

**Important:** Use 6 months, not 90 days. In slower markets, 90 days often returns zero comps. If 6 months still returns < 3 comps, expand to 2-mile radius or include 2-4 bedroom range.

**Extract and organize:**
- 3-5 comparable sales with addresses, prices, $/sqft, dates
- Average $/sqft in the area
- Estimated ARV range (low / mid / high)
- Zillow Zestimate or Redfin estimate if available
- Days on market average for the area

### Layer 4 — Tax & Lien Check (Chrome → County Trustee)

**Action:** Use the Chrome extension to search the county trustee/tax collector site. Use the **parcel number from Layer 1** — it's faster and more accurate than address search.

**Step-by-step Chrome workflow:**

1. **Navigate:** `chrome_navigate → [county trustee tax lookup URL]`
2. **Screenshot** to see the form
3. **Select "Parcel" radio button** if the form offers search-by options
4. **Enter the parcel number** in the search field
5. **Click Search** and wait 3 seconds
6. **Screenshot** the results — look for the property match
7. **Click into the detail page**
8. **Screenshot** the tax detail page

**Extract from trustee:**
- **Tax Due amount** (any amount > $0.00 = potential delinquency)
- **Interest and fees** (interest accruing = definitely delinquent)
- **Total balance owed**
- **Payment history by year** — look for patterns (late payments, skipped years)
- **Assessment values by year** — shows reappraisal history

**For liens (Register of Deeds):**
- If the county has a Register of Deeds GIS or online search, navigate there via Chrome
- Search by parcel number or owner name
- Look for: mortgage recordings, judgment liens, mechanic's liens, lis pendens

**If no online recorder search:** Note as a gap — "Lien search requires in-person or phone request to [County] Register of Deeds at [phone]"

### Layer 5 — Code Violations & Permits

**Some cities have open data APIs that can be queried directly — no Chrome needed.** Check the city-specific instructions below first. If no API exists, fall back to Chrome or phone.

#### Socrata Open Data API — Automated Code Violation Lookup

Several cities publish code violations through Socrata open data APIs. These can be queried directly with WebFetch — no Chrome, no phone call. Check the table below before falling back to Chrome or phone.

**Supported Cities:**

| City | API Endpoint | Dataset ID | Search By | Notes |
|------|-------------|------------|-----------|-------|
| **Dallas, TX** | `dallasopendata.com/resource/x9pz-kdq9.json` | `x9pz-kdq9` | `str_num` + `str_nam` (CAPS, no suffix) | Also has 311 dataset: `eaah-n7x2` (search by `address` LIKE) |
| **New Orleans, LA** | `data.nola.gov/resource/u6yx-v2tw.json` | `u6yx-v2tw` | Check field names with `$limit=1` first run | All code enforcement cases |
| **Los Angeles, CA** | `data.lacity.org/resource/u82d-eh7z.json` | `u82d-eh7z` | Check field names with `$limit=1` first run | Building & Safety code enforcement (open cases) |
| **San Francisco, CA** | `data.sfgov.org/resource/fbaf-fhya.json` | `fbaf-fhya` | Check field names with `$limit=1` first run | Housing code violations (historical) |
| **Buffalo, NY** | `data.buffalony.gov/resource/abwd-pczc.json` | `abwd-pczc` | Check field names with `$limit=1` first run | Active code violations |
| **Mesa, AZ** | `mesa-az.demo.socrata.com/resource/pu8f-55nm.json` | `pu8f-55nm` | Check field names with `$limit=1` first run | Near Phoenix metro |
| **Seattle, WA** | `data.seattle.gov/resource/3qih-4uwq.json` | `3qih-4uwq` | Check field names with `$limit=1` first run | Code compliance violations by year |

**How to query any Socrata endpoint:**

```
Step 1 — First time using a city's API, discover field names:
  WebFetch: https://[domain]/resource/[dataset_id].json?$limit=1
  Prompt: "List ALL field names in this JSON record"

Step 2 — Find the address fields (varies by city):
  Common patterns:
    str_num + str_nam (Dallas)
    address (single field, use $where=address like '%[NUMBER] [STREET]%')
    block + lot (some cities use parcel-based)
    location_address or property_address

Step 3 — Query for the property:
  WebFetch: https://[domain]/resource/[dataset_id].json?[address_field]=[value]&$limit=20
  Prompt: "Return all code violation records with case number, type, status, dates"
```

**Dallas-specific (tested and confirmed):**

```
API 1 — Code Violations (primary):
  URL: https://www.dallasopendata.com/resource/x9pz-kdq9.json?str_num=[HOUSE_NUMBER]&str_nam=[STREET_NAME_UPPERCASE]&$limit=20
  Fields: service_request_id, service_request, nuisance (violation type), status, created, updated

API 2 — 311 Service Requests (supplemental):
  URL: https://www.dallasopendata.com/resource/eaah-n7x2.json?$where=address like '%[HOUSE_NUMBER] [STREET_NAME_UPPERCASE]%'&$limit=20
  Fields: service_request_number, address, service_request_type, status, created_date, update_date
```

**Interpretation (all cities):**
```
IF active/open violations exist →
  Flag: ACTIVE CODE VIOLATIONS
  Add +2 to motivation score
  Note: City may have placed liens for unresolved violations

IF only closed violations →
  Note: "Prior violations (all resolved)" — no motivation score impact

IF no violations found →
  Note: "No code violations on record ([City] OpenData)"
```

#### Chrome Fallback — Accela & Other Portals

If the city is NOT in the Socrata table above, check for a web portal:

- **Dallas Accela:** `aca-prod.accela.com/DALLASTX/` — search by address, shows permits + code cases
- Many cities use Accela, CityView, or similar platforms — search with Perplexity: "[City] code enforcement lookup online" or "[City] Accela portal"
- Use Chrome to navigate the portal, fill the search form, and extract results

#### No API, No Portal — Phone Fallback

Most cities do NOT have an open data API or searchable portal for code violations. For these:

1. Note: "Code violation check requires calling [City] Code Enforcement at [phone]"
2. Alternative: "Submit FOIA/public records request for violation history"

> **When you discover a new city has a Socrata API or other open data source:** Add it to the table above AND to the county config in `./deals/county-configs/`. This turns a phone call into an automated lookup for every future property in that city.
>
> **Found a city with an open data API?** [Open an issue](https://github.com/miron-tech/wholesaler-claude-skills/issues/new?template=new-city-data-source.yml) — takes 30 seconds. We'll add it to the lookup table.

**For building permits:**
- Many counties DO have online permit search (often through Accela or similar platforms)
- Use Perplexity to find: "[County] building permits search online"
- If available, search by address via Chrome
- Recent permits = owner may be mid-renovation (less likely to sell cheap)

### Layer 6 — Neighborhood Intel (Perplexity)

**Run in parallel with Layers 1-2.** Use Perplexity to research the area.

**Prompt:**
> "Real estate market overview for zip code [zip] [City] [State]: median home price, average days on market, year-over-year price trend, rental rates for [beds] bedroom [baths] bathroom homes, investor activity, cap rates, school ratings, flood zone information, and any major developments or changes in the area."

**Extract:**
- Median home price and trend (up/down/flat)
- Average DOM
- Rental rates (for buy-and-hold buyer pitch)
- Sale-to-list ratio (above 95% = strong market)
- New development or investment activity
- Flood risk (check First Street Foundation if Perplexity doesn't have specifics)
- Any red flags (high crime, declining market, environmental issues)

### Layer 7 — Motivation Score & Summary

**Rate the deal opportunity on motivation signals found:**

| Signal | Points | Found? |
|--------|--------|--------|
| Tax delinquent | +3 | |
| Absentee owner | +2 | |
| Out-of-state absentee | +3 | |
| Long-term owner (10+ years) | +2 | |
| Vacant property | +3 | |
| Code violations | +2 | |
| Liens on property | +2 | |
| Entity-owned (LLC/Trust) | +1 | |
| Recent death (probate indicator) | +3 | |
| Pre-foreclosure filing (lis pendens) | +4 | |

```
0-3 points:  LOW MOTIVATION — may not be a motivated seller
4-6 points:  MODERATE — worth reaching out, have a reason to call
7-10 points: HIGH MOTIVATION — prioritize this lead
11+ points:  VERY HIGH — call immediately
```

**Important:** Only score signals you actually confirmed. Mark unverified signals as "Unknown" — don't guess. If most signals are unknown, say so: "Motivation score incomplete — [X] of 10 signals could not be verified."

---

## DECISION LOGIC

```
IF address is in a major metro → County assessor/trustee data is online, use Chrome
IF address is rural/small county → Data may be limited; note gaps in the report
IF entity-owned → Always attempt to pierce the LLC via Secretary of State lookup
IF tax delinquent → Automatically flag as HIGH priority regardless of other signals
IF Firecrawl fails on Redfin → Try Zillow, then Realtor.com
IF Chrome extension is not connected → Note county data as manual steps (provide URLs and phone numbers)
IF multiple parcels at address → Note all parcels and assess each
IF owner-occupied with no motivation signals → Say so honestly — don't force a deal that isn't there
DEFAULT → Run all 7 layers and compile the full report
```

---

## CONSTRAINTS

- [ ] Never present scraped data as guaranteed accurate — always note "per county records as of [date]"
- [ ] Never attempt to find phone numbers or emails (that's skip tracing, not public records)
- [ ] Always note when data could not be found for a layer — don't skip it silently
- [ ] Include the source (Redfin, County Assessor, Perplexity, etc.) for every data point
- [ ] Flag any layer where data seems outdated (e.g., tax records from 2+ years ago)
- [ ] If the property is owner-occupied and has no motivation signals, say so honestly
- [ ] Always verify the address is real before running the full recon (check Redfin/Zillow first)

---

## COMPLETE EXAMPLES

### Example 1 — HIGH MOTIVATION (Dallas, TX)

Shows: PropStream layer, code violations via API, data conflicts, foreclosure discovery, IRA-held property.

> Run a property recon on 3846 Happy Canyon Dr, Dallas, TX 75241.

---

## PROPERTY RECON REPORT
**3846 Happy Canyon Dr, Dallas, TX 75241**
*Generated: March 8, 2026 | Sources: Redfin (Firecrawl), DCAD Assessor (Chrome), Dallas County Tax Office (Chrome), PropStream (Chrome), Dallas OpenData API, Perplexity web search*

---

### PROPERTY DETAILS
| Field | Data | Source |
|-------|------|--------|
| Owner | EQUITY TRUST CUSTODIAN FBO TAMSYN CAMPBELL IRA | DCAD |
| Mailing Address | PO Box 451240, Westlake, OH 44145-0000 | DCAD |
| Property Type | Single-family Residential | DCAD |
| Beds/Baths | 4 / 1 (full), 0 (half) | DCAD |
| Sqft | 1,221 | DCAD |
| Year Built | 1961 | DCAD |
| Lot Size | 7,105 sqft (52ft x 124ft) | DCAD |
| Stories | 1 | DCAD |
| Construction | Frame, Brick Veneer | DCAD |
| Foundation | Slab (DCAD) — Redfin lists Pier & Beam + Slab. Which is correct? | DCAD / Redfin conflict |
| Heating/Cooling | Central Full / Central Full | DCAD |
| Garage | 1 space | Redfin |
| Depreciation | 45% | DCAD |
| Parcel ID | 00000639142000000 | DCAD |

### OWNERSHIP FLAGS
- **ENTITY-OWNED** — held in a self-directed IRA through Equity Trust Company (SDIRA custodian, Westlake OH)
- **OUT-OF-STATE ABSENTEE** — mailing address is Ohio, property is in Dallas TX
- **NOT OWNER-OCCUPIED** — no homestead exemption filed
- **RECENT TRANSFER** — deed transferred 11/13/2025
- **IRA-HELD** — special tax implications (UBIT, prohibited transactions)
- **Beneficial owner:** Tamsyn Campbell

### PROPSTREAM DATA
| Field | Data | Source |
|-------|------|--------|
| Document Type | Trustee's Deed | PropStream |
| Sale Amount | ~$19,000 (foreclosure auction) | PropStream |
| Condition | POOR | PropStream |
| Distressed Status | Foreclosure | PropStream |
| Est. Equity | ~$172,000+ | Calculated |

**FORECLOSURE PURCHASE** — not a voluntary sale. Campbell's IRA acquired at auction for ~10% of market value.

### PRICE HISTORY
| Date | Event | Price |
|------|-------|-------|
| Nov 13, 2025 | Deed Transfer (Trustee's Deed) | ~$19,000 |
| Oct 7, 2025 | Listing Removed | — |
| Oct 3, 2025 | Price Changed | $139,000 |
| Sep 25, 2025 | Listed | $150,000 |
| Feb 22, 2019 | Sold (prior owner) | $124,900 |

### VALUE ESTIMATE
| Source | Value |
|--------|-------|
| Redfin Estimate | $204,635 |
| County Market Value | $191,010 |
| PropStream Avg Comps | ~$165,000 (reference only) |
| Last listed at | $139,000 (failed to sell) |

**Estimated ARV Range:** $180,000 — $210,000
**ARV Confidence:** LOW-MEDIUM

### TAX STATUS (Dallas County Tax Office — as of March 8, 2026)
**Total Amount Due: $3,348.05 — TAXES OWED**

Taxes were due Jan 31, 2026. Penalty and interest accruing. Not severely delinquent (no prior year debt).

### CODE VIOLATIONS (Dallas OpenData API)
| Case # | Type | Status | Date |
|--------|------|--------|------|
| 17-00268731 | Oversized Vehicle | CLOSED | Jun 6, 2017 |

1 violation on record — resolved same day. No active violations.

### NEIGHBORHOOD INTEL (75241)
| Metric | Data | Source |
|--------|------|--------|
| Median Home Value | $206K-$208K | Zillow |
| Median Sale Price | $260K | Redfin |
| YoY Price Trend | Down 1.9% - 5.6% | Redfin/Zillow |
| Days on Market | 46-53 days | Redfin |
| Rental Rate (4BR) | $1,945 - $2,395/mo | Various |

### MOTIVATION SCORE
| Signal | Points | Status |
|--------|--------|--------|
| Tax delinquent (current year) | +1 | YES — $3,348 owed |
| Absentee owner | +2 | YES — OH PO Box |
| Out-of-state absentee | +3 | YES — Westlake, Ohio |
| Vacant property | +3 | LIKELY — IRA-held, no homestead |
| Entity-owned (IRA) | +1 | YES — Equity Trust FBO |
| Failed listing | +2 | YES — $150K→$139K→pulled |
| Code violations | +2 | NO — 1 closed (2017) |
| **TOTAL** | **9 confirmed** | **HIGH MOTIVATION** |

### QUICK NUMBERS (70% Rule)
```
ARV (conservative):     $190,000
MAO (70%):              $133,000
Estimated Repairs:      $25,000-$35,000 (1961, slab, 45% depreciation, POOR condition)
MAO - Repairs:          $98,000 - $108,000
Target Offer Range:     $95,000 - $110,000
Current owner paid:     ~$19,000 (foreclosure auction)
```

### RECOMMENDED NEXT STEPS
1. **Skip trace Tamsyn Campbell** — beneficial IRA owner
2. **Call angle:** "I see you picked up a property at auction in Dallas through your IRA. Taxes are coming due and it needs work — are you looking to move it or hold long-term?"
3. **Offer strategy:** Owner paid ~$19K. Offer $95-110K = 5x+ return on their IRA investment.
4. **IRA note:** All offers go through Equity Trust (custodian). Expect slower process.

---

*Data from DCAD Assessor, Dallas County Tax Office, PropStream, Dallas OpenData, Redfin, and Perplexity as of March 8, 2026. Not a professional appraisal. Verify all data before making offers.*

---

### Example 2 — LOW MOTIVATION (Memphis, TN)

Shows: owner-occupied, taxes current, no code violations portal, honest "skip this lead" verdict.

> Run a property recon on 4973 Cedar View Rd, Memphis, TN 38118.

---

## PROPERTY RECON REPORT
**4973 Cedar View Rd, Memphis, TN 38118**
*Generated: March 3, 2026 | Sources: Redfin, Shelby County Assessor (via Chrome), Shelby County Trustee (via Chrome), Perplexity web search*

---

### PROPERTY DETAILS
| Field | Data | Source |
|-------|------|--------|
| Owner | DAVIS BORIS G | Shelby County Assessor |
| Mailing Address | 4973 Cedar View Rd, Memphis TN 38118-7608 | Shelby County Assessor |
| Property Type | Single Family Residential | County Records |
| Beds/Baths | 3 / 2 | County Records |
| Sqft | 1,375 (ground floor and total living area) | County Records |
| Year Built | 1974 | County Records |
| Lot Size | 8,059 sqft (0.185 acres) | County Records |
| Stories | 1 | County Records |
| Construction | Brick Veneer | County Records |
| Heat/Cool | Central A/C and Heat | County Records |
| Fireplace | 1 (pre-fab) | County Records |
| Basement | None | County Records |
| Parcel ID | 094401 F00046 | County Records |
| Subdivision | Easthaven Rev Sec D, Lot 492 | County Records |
| Land Appraisal | $14,500 | County Assessor |
| Building Appraisal | $105,000 | County Assessor |
| Total Appraisal | $119,500 | County Assessor |
| Total Assessment | $29,875 | County Assessor |
| Last Sale | 01/20/1994 — $55,000 (Deed ED1793, Warranty Deed) | County Records |

### OWNERSHIP FLAGS
- **OWNER-OCCUPIED** — mailing address matches property address
- **LONG-TERM OWNER** — purchased 1994 (32 years)
- **INDIVIDUAL OWNER** — not an LLC, trust, or corporate entity
- **HIGH EQUITY** — bought at $55K, current appraisal $119.5K, no mortgage on record

### SALES HISTORY
| Date | Price | Deed # | Type |
|------|-------|--------|------|
| 01/20/1994 | $55,000 | ED1793 | Warranty Deed |
| 09/29/1986 | $53,209 | Y52904 | Warranty Deed |
| 09/26/1980 | $49,194 | R83325 | Warranty Deed |
| 01/28/1980 | $44,900 | R18987 | Warranty Deed |

### VALUE ESTIMATE
| Comp | Address | Sale Price | Sqft | $/Sqft | Date |
|------|---------|-----------|------|--------|------|
| 1 | 4987 Cedar View Rd | $175,000 | 1,269 | $138 | Sep 15, 2025 |
| 2 | 4693 Spring Valley Dr | $160,000 | N/A | N/A | Recent |
| 3 | 4421 English Pecan Cv | $175,000 | N/A | N/A | Recent |
| 4 | 3380 Spring Water Cv | $135,000 | N/A | N/A | Recent |
| 5 | 3644 Trudy Cv | $129,000 | N/A | N/A | Recent |

**Redfin Estimate:** $152,613 — $159,014
**County Appraisal:** $119,500
**Best Comp (same street):** $138/sqft
**Area Median Sale Price:** $138,000 (Dec 2025)
**Estimated ARV Range:** $145,000 — $165,000
**ARV Confidence:** LOW-MODERATE (1 tight comp with full data, others missing sqft)

### TAX STATUS (Shelby County Trustee — as of March 3, 2026)
**Tax Due: $0.00 | Interest: $0.00 | Total Due: $0.00 — CURRENT**

| Year | Tax Due | Interest | Fees | Total Due | Assessment |
|------|---------|----------|------|-----------|-----------|
| 2025 | $0.00 | $0.00 | $0.00 | $0.00 | 29,875 |
| 2024 | $0.00 | $0.00 | $0.00 | $0.00 | 18,475 |
| 2023 | $0.00 | $0.00 | $0.00 | $0.00 | 18,475 |
| 2022 | $0.00 | $0.00 | $0.00 | $0.00 | 18,475 |
| 2021 | $0.00 | $0.00 | $0.00 | $0.00 | 18,475 |
| 2020 | $0.00 | $0.00 | $0.00 | $0.00 | 14,375 |

Taxes paid in full every year back to 2016 — clean record, no delinquency.

### CODE VIOLATIONS
- **COULD NOT CHECK ONLINE** — City of Memphis has no public code violation search portal
- To check: Call Memphis Code Enforcement at 901-636-7464 or dial 311

### NEIGHBORHOOD INTEL (38118)
| Metric | Data | Source |
|--------|------|--------|
| Median Home Value | $120,618 — $121,916 | Zillow |
| Median Sale Price | $138,000 | Redfin (Dec 2025) |
| Avg Days on Market | 24 days | Redfin |
| YoY Price Trend | +7.3% (Zillow) | Conflicting sources |
| Sale-to-List Ratio | 93.9% | Redfin |
| Market Type | Seller's market | Redfin |
| Rental Rate (3BR) | $1,200 — $1,500/mo | Redfin, Realtor.com |
| Flood Risk | Present — check FEMA + First Street Foundation for parcel-specific | firststreet.org |
| Investor Activity | Active — affordable price point attracts buy-and-hold | Multiple sources |

### MOTIVATION SCORE

| Signal | Points | Status |
|--------|--------|--------|
| Tax delinquent | +3 | **NO** — taxes current |
| Absentee owner | +2 | **NO** — owner-occupied |
| Out-of-state absentee | +3 | **NO** |
| Long-term owner (32 years) | +2 | **YES** |
| Vacant property | +3 | **NO** — owner lives there |
| Code violations | +2 | Unknown — phone check needed |
| Liens on property | +2 | Unknown — recorder check needed |
| Entity-owned (LLC/Trust) | +1 | **NO** — individual |
| Pre-foreclosure (lis pendens) | +4 | Unlikely given clean tax record |
| **TOTAL** | **2 confirmed** | **LOW MOTIVATION** |

### QUICK NUMBERS (70% Rule)
```
Redfin Estimate:        $155,000 (midpoint)
MAO (70%):              $108,500
Estimated Repairs:      $15,000-$25,000 (cosmetic, 1974 build)
MAO - Repairs:          $83,500 - $93,500
Target Offer Range:     $80,000 - $90,000
Assignment Fee at $90K: $10,000-$20,000 depending on buyer
```

### RECOMMENDED NEXT STEPS
1. **Low-priority lead** — owner-occupied, taxes current, no obvious distress signals
2. **If pursuing anyway:** Skip trace Boris G Davis at the Cedar View Rd address
3. **Only angle:** Long-term owner (32 years) may be thinking about retirement/downsizing — but no urgency
4. **Better use of time:** Focus on leads with higher motivation scores (absentee, delinquent, vacant)

---

*Data sourced from Redfin, Shelby County Assessor, Shelby County Trustee, and Perplexity web search as of March 3, 2026. Not a professional appraisal. Verify all data before making offers. County records may not reflect recent changes.*

---

## OUTPUT FORMAT

Deliver recon reports as:
1. **Property Details** — table of facts from county records + Redfin
2. **Ownership Flags** — absentee, entity, long-term, vacancy, owner-occupied
3. **Sales History** — full deed history from county assessor
4. **Value Estimate** — comp table with ARV calculation
5. **Tax Status** — delinquency check from county trustee with payment history
6. **Code Violations** — online check if available, phone number if not
7. **Neighborhood Intel** — market data, rental rates, trends
8. **Motivation Score** — point-based rating with confirmed vs unknown breakdown
9. **Quick Numbers** — 70% rule MAO calculation
10. **Recommended Next Steps** — specific actions based on findings, honest about lead quality

---

## MCP USAGE GUIDE

### Tool Routing — Which MCP For What

| Data Needed | Best Tool | Why |
|------------|-----------|-----|
| Beds, baths, sqft, year built, estimates | **Firecrawl** → Redfin | Static page, scrapes clean with JSON extraction |
| Owner name, mailing address, appraisal | **Chrome** → County Assessor | Interactive form, Firecrawl can't fill forms |
| Tax delinquency, payment history | **Chrome** → County Trustee | Interactive form, requires parcel search |
| Liens, deeds, mortgages | **Chrome** → Register of Deeds | Interactive GIS map, requires parcel/address search |
| Comps, ARV, market data | **Perplexity** | Web search aggregation, good at pulling sold data |
| Neighborhood trends, rental rates | **Perplexity** | Market-level data, not property-specific |
| Code violations | **Chrome** (if online portal exists) or **Phone** | Most cities don't have online lookup |
| LLC/entity piercing | **Firecrawl** → Secretary of State site | Usually a simple static search page |

### Firecrawl Calls

**For Redfin property data (Layer 1):**
```
firecrawl_scrape:
  url: "https://www.redfin.com/[STATE]/[CITY]/[ADDRESS]-[ZIP]/home/[ID]"
  formats: [{ type: "json", prompt: "Extract property details...", schema: {...} }]
  proxy: "stealth"
  waitFor: 5000
```

**For finding Redfin URL:**
```
firecrawl_search:
  query: "[full address] site:redfin.com"
  limit: 3
```

### Chrome Extension Calls

**For county assessor (Layer 2):**
```
1. tabs_context_mcp (createIfEmpty: true)
2. navigate → [assessor URL]
3. screenshot
4. find → "Street Number input field"
5. form_input → ref, value: "[house number]"
6. find → "Street Name input field"
7. form_input → ref, value: "[street name without suffix]"
8. find → "Submit button" (pick the address search one)
9. click → ref (first Submit)
10. wait 3 seconds
11. screenshot → capture results
12. scroll + screenshot → get all sections
13. click to expand: Improvement Details, Sales History, Appraisal sections
14. screenshot each expanded section
```

**For county trustee/tax (Layer 4):**
```
1. navigate → [trustee tax lookup URL]
2. screenshot
3. click → "Parcel" radio button
4. wait 2 seconds
5. screenshot → see parcel search field
6. click the parcel input field
7. type → "[parcel number from Layer 1]"
8. click Search
9. wait 3 seconds
10. screenshot → see results
11. click into the property detail
12. screenshot → capture full tax status and payment history
13. scroll + screenshot for full history
```

### Perplexity Calls

**For comps/value (Layer 3):**
```
perplexity_ask: "What have [beds] bedroom single family homes sold for within
1 mile of [address] in the last 6 months? Include specific addresses, sale
prices, square footage, sale dates, and price per square foot. Also include
the current Zillow Zestimate or Redfin estimate for [address] if available."

search_context_size: "high"
```

**If < 3 comps returned, widen the search:**
```
perplexity_ask: "Recently sold 2-4 bedroom single family homes within 2 miles
of [address] in the last 6 months. List at least 5 with addresses, sale prices,
bedrooms, bathrooms, square footage, and sale dates."

search_context_size: "high"
search_recency_filter: "month"
```

**For neighborhood (Layer 6):**
```
perplexity_ask: "Real estate market overview for zip code [zip] [City] [State]:
median home price, average days on market, year-over-year price trend, rental
rates for [beds] bedroom [baths] bathroom homes, investor activity, cap rates,
flood zone information, and any major developments."

search_context_size: "high"
```

**For county site URLs (if needed):**
```
perplexity_ask: "[County] [State] property tax assessor website - what is the
URL to search property records by address?"
```

---

## PARALLEL EXECUTION PLAN

For maximum speed, run these simultaneously:

**Batch 1 (parallel — no dependencies, just needs the address):**
- Firecrawl → scrape Redfin (Layer 1)
- Perplexity → comps query (Layer 3)
- Perplexity → neighborhood query (Layer 6)
- WebFetch → code violations API if city has Socrata endpoint (Layer 5)
- Chrome → check if PropStream tab exists (detection only)

**Batch 1.5 (parallel with Batch 2, if PropStream detected):**
- Chrome → PropStream property search (Layer 1.5)

**Batch 2 (after Batch 1, needs parcel number):**
- Chrome → county assessor search (Layer 2)
- Chrome → county trustee tax search (Layer 4)

**Batch 3 (after Batch 2, only if needed):**
- Chrome → register of deeds if available (Layer 4 continued)
- Chrome → code violations portal if city has no API (Layer 5 fallback)

**Final:**
- Merge PropStream data with county/Redfin data (cross-reference)
- Compile all data → motivation score → report (Layer 7)
- If PropStream was NOT available → show gap summary with trial link

---

## KNOWN LIMITATIONS BY CITY

| City/County | Assessor Online? | Tax Lookup Online? | Code Violations Online? | Notes |
|-------------|-----------------|-------------------|------------------------|-------|
| Memphis / Shelby County, TN | YES — assessormelvinburgess.com/propertySearch | YES — shelbycountytrustee.com/103/Tax-Look-Up | NO — phone only (901-636-7464) | Don't enter street suffix in assessor search |
| Dallas / Dallas County, TX | YES — dallascad.org/SearchAddr.aspx | YES — dallasact.com/act_webdev/dallas/searchbyproperty.jsp | YES — Dallas OpenData API (no Chrome needed) + Accela portal | See county config: `deals/county-configs/dallas-county-tx.md`. Tax office: skip suffix dropdown (trailing spaces bug). Code violations: use Socrata API `x9pz-kdq9` |
| *Add more cities as tested* | | | | |

> **When testing a new city:** Document the assessor URL, form field names, and any quirks in this table so future runs are faster.

---

## QUALITY CHECKLIST

Before delivering, verify:
- [ ] Is every data point sourced with where it came from?
- [ ] Are gaps clearly noted (not silently skipped)?
- [ ] Is the ARV based on actual comps, not just a Zestimate?
- [ ] Is the motivation score calculated with CONFIRMED signals only?
- [ ] Are unknown signals marked as "Unknown" not assumed?
- [ ] Are the next steps specific and actionable?
- [ ] Is the lead quality assessed honestly (low motivation = say so)?
- [ ] Would a wholesaler feel confident picking up the phone after reading this?
- [ ] Is there a disclaimer about data accuracy?
- [ ] Was the address verified as real before running the full recon?
