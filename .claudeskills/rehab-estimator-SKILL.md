---
name: rehab-estimator
description: "Generate a photo-based rehab estimate for any property. Accepts photos from listing sites (Redfin/Zillow via Chrome), a local folder on your computer, or a shared Google Drive link. Use when a wholesaler needs repair cost estimates before making an offer, building a deal package, or validating their numbers. Grades property condition across 6 zones using the R.E.H.A.B.+F scoring framework and produces three-scenario rehab budgets (rental-ready, mid-range flip, full worst-case). Uses Chrome MCP for Redfin photo browsing, Perplexity for local contractor costs, and Firecrawl for finding listing URLs."
---

# Rehab Estimator Skill

> **Purpose:** Turn property photos into a defensible, line-item rehab estimate in three scenarios — so you know your repair number before you make an offer, not after. Works with listing photos, your own photos from a walkthrough, or photos shared via Google Drive.

---

## PREREQUISITE
- **Claude in Chrome MCP** — for browsing Redfin/Zillow photo galleries (not needed if using local photos or Google Drive)
- **Perplexity MCP** — local contractor cost data, material pricing by market, cost adjustments
- **Firecrawl MCP** — finding correct Redfin/Zillow listing URLs (not needed if using local photos or Google Drive)

> **No listing? No problem.** This skill works with three photo sources: (1) Listing sites like Redfin/Zillow via Chrome, (2) a folder of photos on your computer, or (3) photos shared via Google Drive link. If you drove for dollars and took photos on your phone, just drop them in a folder and point Claude at it.

---

## WHAT THIS SKILL DOES

- Analyzes property photos from any source — Redfin/Zillow listings, local folders, or Google Drive
- Grades property condition across 6 zones using the R.E.H.A.B.+F scoring framework
- Produces line-item repair estimates for every visible deficiency
- Outputs three budget scenarios: rental-ready, mid-range flip, and full worst-case
- Flags hidden cost risks that photos can't confirm (asbestos, galvanized pipes, foundation issues)
- Calculates deal impact using the 70% rule at both mid-range and worst-case repair numbers
- Integrates with Property Recon data (year built, foundation type, HVAC type) for smarter estimates

## WHAT THIS SKILL DOES NOT DO

- Does not replace a physical inspection — photos miss what's behind walls
- Does not guarantee repair costs — estimates based on visible conditions and market averages
- Does not assess structural integrity from photos alone — always flags foundation/structural as "verify on-site"
- Does not pull permits or inspection reports (use Property Recon for that)
- Does not provide contractor referrals or schedule work

---

## INPUTS NEEDED

| Input | Required | Example |
|-------|----------|---------|
| Property address | Yes | "9631 Silver Meadow Dr, Dallas, TX 75217" |
| Property details | Yes (or pull from Property Recon) | "2/1, 1,008 sqft, built 1959, pier & beam" |
| **Photo source (one of these):** | Yes | See below |
| — Listing URL (Redfin preferred) | Option A | Redfin or Zillow listing URL (Chrome browses the gallery) |
| — Local photo folder | Option B | "/Users/you/photos/123-oak-st/" — your own photos from a walkthrough or D4D |
| — Google Drive link | Option C | A shared Google Drive folder link — download photos first, then analyze |
| Property Recon data | Helpful | Year built, foundation type, HVAC type, construction, roof type |
| Purpose | Helpful | "Making an offer" or "Building a deal package" or "Deciding cash vs creative" |
| ARV (from Comp Analyzer) | Helpful | "$195,000" — used for deal impact calculation |

---

## GOAL

- Primary: Give the wholesaler a defensible repair estimate they can use to calculate MAO, build buyer packages, and negotiate with confidence
- Secondary: Identify the 3-5 biggest cost drivers so the wholesaler knows where the money goes
- Tertiary: Flag hidden risks that could blow up the budget post-inspection

---

## SKILL CHAIN INTEGRATION

Rehab Estimator sits between Property Recon and Deal Stacker in the chain.

### Where It Fits

```
Property Recon → Auto-Comp Analyzer → Rehab Estimator → Creative Finance → Conversation Coach → Deal Stacker → CyclSales Connect
```

### Data That Flows In

| If This Skill Was Run | Data Available | How to Use It |
|-----------------------|---------------|---------------|
| Property Recon | Year built, foundation type, HVAC type, construction, roof type, sqft, beds/baths, county appraisal | Pre-populates property details. Year built drives hidden risk flags. Foundation type adjusts plumbing/HVAC/structural estimates. |
| Auto-Comp Analyzer | ARV, as-is value, flip activity | Use ARV for deal impact calculation. Flip sale prices validate rehab scope (if flippers spent $80K on rehab, your estimate should be in range). |

### Data That Flows Out

| Receiving Skill | What It Gets | How It Uses It |
|----------------|-------------|----------------|
| Deal Stacker | Three-scenario repair estimates (low/mid/high) | Plugs into SPREAD scoring for MAO calculation. Uses mid-range for primary, worst-case for conservative. |
| Auto-Comp Analyzer | Verified repair estimate | Replaces guessed repair number in 70% rule MAO calculation. |
| Creative Finance | Repair cost for cash-on-cash ROI calculation | Factors rehab into total investment for creative deal structuring. |
| Conversation Coach | Key repair items and cost drivers | Arms the wholesaler with specific repair items to justify their offer price to the seller. |

### Rules for Using Chain Data
```
IF Property Recon was run → Use its year built, foundation, HVAC, construction data (don't re-research)
IF Comp Analyzer was run → Use its ARV for deal impact section
IF neither was run → Ask user for property details, or scrape Redfin for basics before photo review
IF Property Recon shows "pier & beam" foundation → Adjust plumbing and HVAC estimates (easier access)
IF Property Recon shows "slab" foundation → Flag plumbing repipe as significantly more expensive
IF Property Recon shows year built < 1978 → Auto-flag asbestos and lead paint risk
IF Property Recon shows "gas heaters" or "window units" → HVAC is guaranteed major line item
```

---

## FRAMEWORK: The R.E.H.A.B.+F Score (6-Zone Photo Walkthrough)

Each zone gets a condition grade from 1-4. Total score (6-24) maps to overall rehab severity.

### Grading Scale

| Grade | Meaning | What You See |
|-------|---------|-------------|
| 1 — Good | Minor cosmetic only | Clean, functional, dated but serviceable. Paint and maybe flooring. |
| 2 — Fair | Moderate updates needed | Worn but intact. Needs updating but not gutting. Functional but ugly. |
| 3 — Poor | Major renovation required | Damaged, non-functional, or severely outdated. Gut and replace. |
| 4 — Critical | Full replacement / structural concern | Dangerous, failed, or missing entirely. System replacement required. |

### Zone Details

#### Zone R — Roof, Exterior & Envelope
**What to look for in photos:**
- Roof: missing/curling shingles, sagging ridgeline, visible patches, moss/algae
- Siding: cracks, holes, peeling paint, rot, damaged sections
- Windows: single-pane, broken seals (fogging), cracked frames, diamond-pattern (old)
- Doors: damaged entry door, worn weather stripping, broken storm door
- Carport/garage: structural issues, damaged door
- Yard/hardscape: overgrown, broken walkways, damaged fence, drainage issues

**Default cost table (DFW 2025-2026 baseline):**

| Item | Rental-Ready | Mid-Range Flip | Worst-Case |
|------|-------------|----------------|-----------|
| Roof replacement (comp shingle) | $0 (patch only: $500) | $6,000-$8,000 | $8,000-$10,000 |
| Exterior paint + siding repair | $1,500-$2,500 | $3,000-$5,000 | $5,000-$7,000 |
| Windows (per window, vinyl) | $0 (skip) | $300-$500 each | $400-$600 each |
| Entry door replacement | $0 (keep) | $500-$800 | $800-$1,200 |
| Back/side door replacement | $0 (keep) | $300-$500 | $500-$800 |
| Garage door replacement | $0 (keep) | $800-$1,200 | $1,200-$1,800 |
| Fence repair/replace (chain link) | $300-$500 | $500-$1,000 | $2,000-$4,000 (wood) |
| Landscaping/yard cleanup | $300-$500 | $500-$1,000 | $1,000-$2,000 |
| Dumpster + estate cleanout | $800-$1,200 | $1,000-$1,500 | $1,500-$2,000 |

#### Zone E — Electrical & HVAC
**What to look for in photos:**
- HVAC: wall heaters (no central), window AC units, old furnace, visible ductwork condition
- Electrical panel: old fuse box vs breaker panel, Federal Pacific (fire risk), Zinsco
- Outlets: two-prong (no ground), missing covers, old style
- Light fixtures: dated, missing, non-functional
- Ceiling fans: working or not

**Default cost table:**

| Item | Rental-Ready | Mid-Range Flip | Worst-Case |
|------|-------------|----------------|-----------|
| Central HVAC install (no existing) | $8,000-$10,000 | $10,000-$14,000 | $12,000-$16,000 |
| HVAC replacement (existing system) | $4,000-$6,000 | $5,000-$7,000 | $7,000-$9,000 |
| Ductwork (new, pier & beam) | $2,000-$3,000 | $3,000-$4,000 | $4,000-$5,000 |
| Ductwork (new, slab/attic) | $3,000-$4,000 | $4,000-$5,000 | $5,000-$7,000 |
| Panel upgrade (100A→200A) | $1,500-$2,000 | $1,800-$2,500 | $2,500-$3,500 |
| Partial rewire (kitchen, bath, HVAC circuits) | $1,500-$2,500 | $2,000-$3,000 | $3,000-$4,000 |
| Full rewire (whole house) | N/A | $4,000-$6,000 | $6,000-$10,000 |
| Light fixtures + ceiling fans | $300-$500 | $500-$1,000 | $1,000-$1,500 |
| GFCI outlets (kitchen, bath, exterior) | $200-$400 | $300-$500 | $500-$800 |

#### Zone H — Hydro (Plumbing)
**What to look for in photos:**
- Water damage: ceiling stains, wall discoloration, bubbling paint, warped flooring
- Mold: dark spots on ceiling/walls near wet areas, musty-looking surfaces
- Water heater: visible age, rust, location
- Fixtures: corroded faucets, stained sinks, old toilets
- Under-sink: visible pipes (galvanized = silver/gray, copper = copper, PEX = plastic)

**Default cost table:**

| Item | Rental-Ready | Mid-Range Flip | Worst-Case |
|------|-------------|----------------|-----------|
| Water heater replacement | $800-$1,200 | $1,000-$1,500 | $1,500-$2,000 (tankless) |
| Fixture replacement (per bathroom) | $300-$500 | $500-$800 | $800-$1,200 |
| Kitchen faucet + garbage disposal | $200-$400 | $300-$500 | $500-$800 |
| Water damage repair (per area) | $500-$1,000 | $1,000-$2,000 | $2,000-$4,000 |
| Mold remediation (per area) | $500-$1,000 | $1,000-$3,000 | $3,000-$5,000 |
| Galvanized repipe (pier & beam) | N/A | $4,000-$6,000 | $6,000-$8,000 |
| Galvanized repipe (slab) | N/A | $6,000-$8,000 | $8,000-$12,000 |
| Sewer line repair/replace | N/A | $2,000-$4,000 | $4,000-$8,000 |
| Drain cleaning / camera scope | $200-$400 | $300-$500 | $500-$800 |

#### Zone A — All Interior Surfaces
**What to look for in photos:**
- Walls: wood paneling, outdated wallpaper, damaged drywall, cracks, holes
- Ceilings: popcorn/textured (asbestos risk pre-1978), water stains, sagging
- Flooring: worn carpet, damaged vinyl/linoleum, outdated tile, subfloor damage
- Trim/baseboards: missing, damaged, outdated
- Interior doors: hollow-core, damaged, missing
- Paint: peeling, stained, dark/outdated colors

**Default cost table:**

| Item | Rental-Ready | Mid-Range Flip | Worst-Case |
|------|-------------|----------------|-----------|
| Wood paneling removal + drywall | $1,500-$2,500 | $3,000-$5,000 | $5,000-$7,000 |
| Popcorn ceiling removal (no asbestos) | $1,000-$1,500 | $1,500-$2,500 | $2,500-$3,500 |
| Popcorn ceiling removal (with abatement) | N/A | $3,000-$5,000 | $5,000-$7,000 |
| Wallpaper removal + skim coat | $500-$1,000 | $1,000-$2,000 | $2,000-$3,000 |
| Drywall repair (patches, cracks) | $300-$500 | $500-$1,000 | $1,000-$2,000 |
| Interior paint (full house) | $1,500-$2,000 | $2,000-$3,000 | $3,000-$4,000 |
| LVP flooring (installed, per sqft) | $3.50-$4.50 | $4.00-$6.00 | $5.00-$7.00 |
| Carpet (installed, per sqft) | $2.00-$3.00 | N/A (use LVP) | N/A |
| Tile flooring (installed, per sqft) | N/A | $6.00-$8.00 | $8.00-$12.00 |
| Subfloor repair (per area) | $500-$1,000 | $1,000-$2,000 | $2,000-$4,000 |
| Interior doors (per door, installed) | $0 (keep) | $150-$250 | $200-$350 |
| Trim/baseboards (per LF) | $0 (keep) | $2.00-$3.00 | $3.00-$5.00 |

#### Zone B — Bathrooms & Kitchen
**What to look for in photos:**
- Kitchen: cabinet condition, countertop material, appliance age/condition, backsplash, layout
- Bathroom: vanity condition, tub/shower (surround vs tile), toilet age, tile condition, exhaust fan
- Both: faucet style/condition, lighting, ventilation

**Default cost table:**

| Item | Rental-Ready | Mid-Range Flip | Worst-Case |
|------|-------------|----------------|-----------|
| Kitchen cabinets (paint existing) | $1,000-$1,500 | N/A | N/A |
| Kitchen cabinets (new shaker, stock) | N/A | $4,000-$6,000 | $6,000-$8,000 |
| Kitchen countertops (laminate) | $500-$800 | N/A | N/A |
| Kitchen countertops (granite/quartz) | N/A | $2,000-$3,000 | $3,000-$4,500 |
| Kitchen backsplash (subway tile) | $0 (skip) | $500-$800 | $800-$1,200 |
| Kitchen sink + faucet | $200-$400 | $300-$500 | $500-$800 |
| Appliance package (range, micro, DW, fridge) | $1,500-$2,500 | $2,500-$3,500 | $3,500-$5,000 |
| Bathroom vanity + mirror | $300-$500 | $500-$800 | $800-$1,200 |
| Tub/shower surround (acrylic insert) | $500-$800 | N/A | N/A |
| Tub/shower surround (tile) | N/A | $1,500-$2,500 | $2,500-$4,000 |
| Toilet replacement | $150-$250 | $200-$300 | $300-$500 |
| Bathroom tile floor | $300-$500 | $500-$800 | $800-$1,200 |
| Exhaust fan | $100-$200 | $150-$250 | $200-$350 |

#### Zone +F — Foundation & Structure
**What to look for in photos:**
- Foundation: visible cracks in brick/slab, uneven floors (furniture gaps), sticking doors
- Pier & beam: visible pier damage, sagging floors, access point condition
- Structural walls: cracks above door frames, diagonal cracks, bowing walls
- Termite damage: wood damage at base, mud tubes, sawdust piles
- Subfloor: bouncy/soft spots (visible in walking videos)

**Default cost table:**

| Item | Rental-Ready | Mid-Range Flip | Worst-Case |
|------|-------------|----------------|-----------|
| Pier & beam leveling (minor) | $1,500-$3,000 | $2,000-$4,000 | $4,000-$6,000 |
| Pier & beam leveling (major) | N/A | $4,000-$6,000 | $6,000-$10,000 |
| Slab foundation repair (piers) | N/A | $4,000-$8,000 | $8,000-$15,000 |
| Subfloor replacement (per area) | $500-$1,000 | $1,000-$2,000 | $2,000-$4,000 |
| Termite treatment | $500-$800 | $800-$1,200 | $1,200-$2,000 |
| Termite damage repair | $500-$1,500 | $1,500-$3,000 | $3,000-$8,000 |
| Structural wall repair | N/A | $2,000-$4,000 | $4,000-$8,000 |
| Crawl space moisture barrier | $500-$1,000 | $1,000-$2,000 | $2,000-$3,000 |

### R.E.H.A.B.+F Score Interpretation

| Total Score (6-24) | Severity | Typical Budget Range (DFW, ~1,000 sqft) |
|--------------------|----------|----------------------------------------|
| 6-9 | **COSMETIC** — paint, flooring, fixtures | $8,000-$20,000 |
| 10-13 | **LIGHT REHAB** — kitchen/bath refresh, some systems | $20,000-$35,000 |
| 14-17 | **MODERATE REHAB** — gut kitchen/bath, HVAC, surfaces | $35,000-$55,000 |
| 18-21 | **HEAVY REHAB** — full gut, all systems, structural possible | $55,000-$80,000 |
| 22-24 | **DOWN TO STUDS** — complete teardown and rebuild interior | $80,000-$120,000+ |

---

## DECISION LOGIC

```
IF year built < 1978 AND popcorn ceilings visible →
  Flag: ASBESTOS RISK — add $2K-$3K abatement to mid-range, $3K-$5K to worst-case
  Action: "Test before touching. DO NOT scrape without testing."

IF year built < 1960 AND no visible electrical panel upgrade →
  Flag: ORIGINAL WIRING — likely needs partial or full rewire
  Action: Add $2K-$4K (partial) or $4K-$10K (full) to estimates

IF foundation type = "pier & beam" →
  Adjustment: Plumbing repipe is CHEAPER (easier access), HVAC ductwork goes under house
  Note: Pier leveling is common on 50+ year homes — budget $2K-$6K unless photos show severe issues

IF foundation type = "slab" →
  Adjustment: Plumbing repipe is MORE EXPENSIVE, any foundation work requires tunneling
  Note: Slab foundation cracks/movement = $8K-$15K+ and deal-breaker territory

IF HVAC shows "gas heaters" or "window units" (no central system) →
  Flag: HVAC IS THE #1 EXPENSE — $10K-$16K for new system + ductwork
  This is always the biggest single line item on pre-1970 homes

IF only 1 bathroom AND property is 3+ bedrooms →
  Note: Adding a 2nd bathroom = $8K-$15K depending on plumbing access
  This dramatically increases ARV on older 3/1 homes

IF listing says "sold as-is" or "investor special" →
  Assume MODERATE to HEAVY rehab minimum
  Sellers use this language when they know the problems are significant

IF photos show water staining on ceiling near bathroom →
  Flag: ACTIVE LEAK OR PREVIOUS LEAK — investigate source
  Budget $500-$4,000 depending on extent (surface stain vs mold/rot)

IF photos show wood paneling on walls →
  Budget for removal + drywall + texture + paint ($3K-$7K for typical house)
  OR paint over for rental-ready ($500-$1,500)

IF no interior photos available →
  Cannot estimate interior rehab — note as "INTERIOR UNKNOWN, budget worst-case"
  Use year built + exterior condition to estimate severity range

IF exterior only (no listing, just D4D or drive-by) →
  Grade Zone R from exterior photos, flag all interior zones as UNKNOWN
  Provide exterior-only estimate + range for interior based on year/condition

IF user provides a local folder path →
  Use the Read tool to open each image file in the folder (JPG, PNG, HEIC)
  Claude can see photos directly — no Chrome needed
  Analyze each photo the same way as listing photos
  This is the BEST source — walkthrough photos often show more than listing photos

IF user provides a Google Drive link →
  Google Drive links require downloading first. Tell the user:
  "Download the photos from Google Drive to a folder on your computer, then give me the folder path."
  OR if the link is a public share, try using Firecrawl to access individual image URLs
  Once photos are local, use the Read tool to analyze each one

DEFAULT →
  Browse all available photos, grade all 6 zones, produce three-scenario estimate
```

---

## MCP USAGE GUIDE

### Tool Routing — Which MCP For What

| Data Needed | Best Tool | Why |
|------------|-----------|-----|
| Photos from a listing site | **Chrome** → Redfin/Zillow photo gallery | Interactive gallery requires clicking tabs, scrolling, navigating |
| Photos from a local folder | **Read tool** (built-in, no MCP needed) | Claude reads image files directly — JPG, PNG, HEIC |
| Photos from Google Drive | **Download first**, then **Read tool** | Google Drive links need to be downloaded to a local folder first |
| Correct listing URL | **Firecrawl** search | Redfin URLs need home IDs — search first |
| Property details (beds, baths, sqft, year built) | **Property Recon** data or **Firecrawl** → Redfin | Get basics before photo review |
| Local contractor costs by market | **Perplexity** | "Average cost of HVAC installation in [city] [state] 2025-2026" |
| Material costs by market | **Perplexity** | "Cost of LVP flooring installed per sqft in [city] 2025-2026" |
| Foundation type, HVAC type | **Property Recon** data or **Chrome** → county assessor | DCAD, Shelby County, etc. have improvement details |

### Chrome Extension Calls — Photo Gallery Workflow

**Step 1: Get to the listing**
```
1. tabs_context_mcp (createIfEmpty: true)
2. tabs_create_mcp → new tab
3. navigate → Redfin listing URL
4. wait 3 seconds
5. screenshot → verify the listing loaded
```

**Step 2: Open photo gallery**
```
6. find → "Photos" or "See all photos" or photo count button
7. click → open the full photo gallery
8. wait 2 seconds
9. screenshot → see the gallery layout
```

**Step 3: Browse by category (Redfin organizes photos by room type)**
```
For each category tab available (Kitchen, Bathroom, Bedroom, Living Room, Dining, Exterior, etc.):
10. find → category tab name
11. click → switch to that category
12. wait 1 second
13. screenshot → capture first photo in category
14. find → "Next" arrow or right arrow
15. click through remaining photos in category, screenshot each
```

**Step 4: Capture exterior and uncategorized**
```
16. find → "All" tab or scroll to exterior/yard photos
17. screenshot exterior front, back, sides, yard, carport/garage
18. Look for: roof condition, siding, windows, fence, landscaping
```

**Step 5: Look for detail shots**
```
19. Check for: HVAC equipment, water heater, electrical panel, foundation/crawl space
20. These are rare in listings but invaluable when present
21. Screenshot any mechanical/system photos
```

**If Redfin gallery uses a different layout (carousel vs grid):**
- Try clicking the main photo to enter fullscreen gallery mode
- Use arrow keys (press_key → "ArrowRight") to cycle through all photos
- Screenshot every 2-3 photos to catch all rooms

**If no photos on Redfin:** Try Zillow, Realtor.com, or MLS listing (some have more photos)

### Local Folder Workflow — Your Own Photos

When the user has their own photos (from a walkthrough, D4D drive-by, or sent by a seller/agent), they don't need Chrome or Firecrawl at all. Claude reads image files directly.

**Step 1: Get the folder path**
```
User provides: "Photos are in ~/Desktop/123-oak-st-photos/"
Or: "I put the photos in /Users/me/Downloads/property-photos/"
```

**Step 2: List all image files in the folder**
```
Use Glob tool: "~/Desktop/123-oak-st-photos/*.{jpg,jpeg,png,heic,JPG,JPEG,PNG,HEIC}"
This finds every photo in the folder
```

**Step 3: Read each photo**
```
Use Read tool on each image file — Claude sees the photo visually
Read all photos (or batch them if there are many)
Analyze each one for condition, damage, finishes, systems
```

**Step 4: Grade and estimate**
```
Same R.E.H.A.B.+F grading as listing photos
Same three-scenario budget output
```

**Tips for better results with your own photos:**
- Label photos by room if possible (kitchen-1.jpg, bathroom-1.jpg, exterior-front.jpg)
- Take photos of: every room, the roof (from the yard), the electrical panel, the water heater, under sinks, HVAC equipment
- Close-ups of damage are more useful than wide shots of clean rooms
- Even phone photos work — Claude can read any image format

### Google Drive Workflow — Shared Photos

When photos are shared via Google Drive (common when a seller, agent, or bird dog sends photos):

**Option A: Download first (recommended)**
```
1. Tell the user: "Download those photos from Google Drive to a folder on your computer"
   - In Google Drive: select all photos → right-click → Download (creates a .zip)
   - Unzip to a folder
2. User provides the local folder path
3. Follow the Local Folder Workflow above
```

**Option B: Public Google Drive link**
```
1. If the link is publicly shared, try using Firecrawl to access the folder listing
2. This is less reliable than downloading — Google Drive pages are JavaScript-heavy
3. If Firecrawl can't read it, fall back to Option A
```

**Always recommend Option A** — downloading is faster and more reliable than trying to scrape Google Drive.

### Perplexity Calls — Cost Adjustment

**For market-specific costs:**
```
perplexity_ask: "What is the average cost in [city], [state] in 2025-2026 for:
1. Central HVAC installation (new system with ductwork) for a [sqft] sqft single family home
2. Kitchen renovation (mid-grade, new cabinets, countertops, appliances)
3. Bathroom renovation (mid-grade, new vanity, tub surround, tile, toilet)
4. LVP flooring installation per square foot
5. Interior paint per square foot
6. Roof replacement (comp shingle) for [sqft] sqft home
Include both labor and material costs."

search_context_size: "high"
```

### Firecrawl Calls — Finding Listing URLs

**Find the Redfin listing URL:**
```
firecrawl_search: "[full address] site:redfin.com"
→ Returns correct Redfin URL with home ID
```

**If no Redfin listing, try Zillow:**
```
firecrawl_search: "[full address] site:zillow.com"
```

---

## PARALLEL EXECUTION PLAN

**Batch 1 — Find listing + gather context (run simultaneously):**
- Firecrawl: Search for Redfin listing URL
- Perplexity: Local contractor/material costs for the market
- (If Property Recon data not available) Perplexity: Property details query

**Batch 2 — Photo walkthrough (sequential, Chrome-dependent):**
- Chrome: Navigate to Redfin listing
- Chrome: Open photo gallery
- Chrome: Browse each category tab, screenshot all photos
- Chrome: Capture exterior, yard, mechanical photos
- (This batch is inherently sequential — you click through one photo at a time)

**Batch 3 — Analysis (no tool calls):**
- Grade each of the 6 R.E.H.A.B.+F zones from photo evidence
- Build line-item cost table using local market costs from Batch 1
- Calculate three scenarios (rental-ready, mid-range, worst-case)
- Flag hidden cost risks based on year built + visible conditions
- Calculate deal impact (70% rule with repair estimates)

**Batch 4 — Report (no tool calls):**
- Compile full report with zone grades, line items, scenarios, risks, deal impact
- Cross-reference with Comp Analyzer data if available (do flip rehab costs align?)

---

## COST ADJUSTMENT BY MARKET

DFW is the baseline. Multiply all cost estimates by the market factor for other metros.

| Market | Cost Factor | Notes |
|--------|------------|-------|
| **DFW (Dallas-Fort Worth)** | 1.00x (baseline) | Strong contractor availability, competitive pricing |
| **Memphis, TN** | 0.80-0.85x | Lower labor costs, lower COL. Abundant rehab inventory. |
| **Houston, TX** | 0.95-1.00x | Similar to DFW, slightly lower in suburbs |
| **San Antonio, TX** | 0.90-0.95x | Lower than DFW, good contractor pool |
| **Phoenix, AZ** | 1.05-1.10x | Higher labor demand, HVAC is always needed |
| **Atlanta, GA** | 0.90-0.95x | Competitive market, good labor supply |
| **Indianapolis, IN** | 0.80-0.85x | Low COL, abundant contractors |
| **Kansas City, MO** | 0.80-0.85x | Similar to Indianapolis |
| **Jacksonville, FL** | 0.90-0.95x | Moderate costs, hurricane code adds to roofing |
| **Cleveland, OH** | 0.75-0.80x | Lowest rehab costs in major investor markets |
| **Tampa, FL** | 1.00-1.05x | Higher demand, hurricane code compliance |
| **Los Angeles / SoCal** | 1.40-1.60x | Permits, labor, everything costs more |
| **NYC / NJ metro** | 1.50-1.70x | Highest labor costs in the country |

**How to use:** If Perplexity returns local costs, use those. If not, multiply DFW baseline costs by the market factor. Always note which method was used.

---

## HIDDEN COST RISKS TABLE

These are items photos CANNOT confirm but that frequently blow up rehab budgets. Flag based on property characteristics.

| Hidden Risk | Trigger (When to Flag) | Probability | Cost If Found |
|-------------|----------------------|-------------|---------------|
| **Asbestos (popcorn ceilings, tile, insulation)** | Year built < 1978 + popcorn ceilings or 9x9 floor tile visible | HIGH (pre-1978) | $2,000-$7,000 abatement |
| **Lead paint** | Year built < 1978 + original paint visible | MODERATE (pre-1978) | $1,500-$5,000 remediation |
| **Galvanized pipes** | Year built < 1970 + no visible repipe evidence | HIGH (pre-1960), MODERATE (1960-1970) | $4,000-$12,000 repipe |
| **Polybutylene pipes** | Year built 1978-1995 + gray plastic pipes visible | MODERATE | $4,000-$8,000 repipe |
| **Foundation movement** | Year built 50+ years + slab foundation + Texas clay soil | MODERATE-HIGH (TX) | $4,000-$15,000 piers |
| **Termite damage** | Wood frame + pier & beam + visible wood contact with soil | MODERATE (South/SE US) | $1,500-$10,000 |
| **Sewer line (clay/cast iron)** | Year built < 1970 + original plumbing | MODERATE | $3,000-$8,000 replacement |
| **Knob-and-tube wiring** | Year built < 1940 | HIGH (pre-1940) | $8,000-$15,000 full rewire |
| **Aluminum wiring** | Year built 1965-1973 | MODERATE | $3,000-$8,000 pigtailing or rewire |
| **Roof deck damage** | Visible roof sag, old shingles, water stains in attic area | LOW-MODERATE | $2,000-$5,000 on top of reroof |
| **Cast iron drain pipes** | Year built < 1975 + original plumbing | MODERATE | $3,000-$6,000 replacement |
| **Mold behind walls** | Visible water damage + musty smell reported + humid climate | MODERATE | $2,000-$10,000 remediation |

---

## CONSTRAINTS

- [ ] Always note which items are CONFIRMED by photos vs ASSUMED based on age/type
- [ ] Never present a single number — always show a range (low-high) for every line item
- [ ] Always produce all three scenarios (rental-ready, mid-range, worst-case)
- [ ] Flag the top 3-5 biggest cost drivers explicitly — don't bury them in a long table
- [ ] Include hidden cost risks for every property — photos can't see everything
- [ ] If no interior photos are available, say so — don't guess interior condition
- [ ] Note the market and cost adjustment factor used
- [ ] Include a disclaimer: "Estimate based on listing photos only. Physical inspection required."
- [ ] If HVAC is wall heaters / window units, it MUST be flagged as the #1 expense
- [ ] Cross-reference rehab scope with flip sale data if Comp Analyzer was run — do the numbers make sense?
- [ ] Always calculate deal impact (70% rule) at both mid-range and worst-case repair numbers

---

## COMPLETE EXAMPLE (Real Data)

### Example Input:
> Run a rehab estimate on 9631 Silver Meadow Dr, Dallas, TX 75217. Property Recon already run — 2/1 (3/1 per county), 1,008 sqft, built 1959, pier & beam, gas heaters + window AC, frame construction, comp shingle roof, 45% depreciation per DCAD. Listed on Redfin as investor special at $145K. ARV from Comp Analyzer: $175K-$215K (midpoint $195K).

### Example Output:

---

## PHOTO-BASED REHAB ESTIMATE
**9631 Silver Meadow Dr, Dallas, TX 75217 | 2/1 (3/1 per DCAD), 1,008 sqft, 1959, Pier & Beam**
*Generated: March 4, 2026 | Sources: Redfin listing photos (24 photos reviewed via Chrome), DCAD property data, Perplexity cost data*

---

### R.E.H.A.B.+F ZONE GRADES

| Zone | Grade | Key Findings |
|------|-------|-------------|
| **R** — Roof, Exterior & Envelope | **2 — Fair** | White horizontal siding worn, needs paint, some damage near back door. Comp shingle roof age unknown but appears serviceable. Metal carport functional. Large fenced yard. |
| **E** — Electrical & HVAC | **4 — Critical** | **NO central HVAC.** Gas wall heaters + window AC units only. Old electrical outlets, likely original 1959 wiring. Panel upgrade needed for HVAC circuits. |
| **H** — Hydro (Plumbing) | **3 — Poor** | **Visible water damage/dark staining on bathroom ceiling near shower.** Mold risk. Fixtures are original. Galvanized pipes likely (1959 build). |
| **A** — All Interior Surfaces | **3 — Poor** | **Wood paneling on every wall** in living room, kitchen, dining, hallway — full removal needed. Popcorn/textured ceilings throughout (1959 = asbestos risk). Heavily worn/stained carpet. Vinyl/linoleum in kitchen. |
| **B** — Bathrooms & Kitchen | **3 — Poor** | **Kitchen is 100% original** — wood cabinets, laminate counters, no dishwasher, old gas stove. Only 1 bathroom with dated fixtures and water damage. |
| **+F** — Foundation & Structure | **2 — Fair** | Pier & beam at 67 years. No visible severe settling in photos. Floors appear reasonably level. Must verify on-site. |

**R.E.H.A.B.+F TOTAL: 17 / 24 — MODERATE REHAB**

---

### WHAT THE PHOTOS TELL ME

**Exterior:**
- White horizontal siding — worn, needs paint, some damage near back door
- Comp shingle roof — age unknown, appears serviceable but 67-year-old house
- Metal carport (functional), attached garage (unfinished inside)
- Large concrete patio in back, big fenced yard
- No major structural red flags visible from outside

**Interior:**
- **Wood paneling on every wall** in living room, kitchen, dining, hallway — full removal needed
- **Popcorn/textured ceilings throughout** — 1959 build = almost certainly contains asbestos
- **Kitchen is 100% original** — wood cabinets, laminate counters, no dishwasher, old freestanding gas stove
- **Only 1 bathroom** — dated fixtures, and **visible water damage/dark staining on ceiling near shower** (mold risk)
- **Gas wall heaters** (no central heat) + **window AC units** (no central AC) — HVAC is the #1 expense
- **Carpet is heavily worn/stained** in living areas and one bedroom; vinyl/linoleum in kitchen/dining
- Bedrooms have painted walls (not paneled) — in better shape
- **One bedroom converted to 2nd living area/office** (explaining the 2-bed listing vs 3-bed county record)
- Old electrical outlets, likely original wiring

---

### LINE-ITEM REHAB BUDGET

| Item | Rental-Ready | Mid-Range Flip | Worst-Case | Notes |
|------|-------------|----------------|-----------|-------|
| **HVAC (central AC + furnace + new ductwork)** | $10,000 | $12,000 | $14,000 | Biggest single expense. Pier & beam = duct under house, easier than slab |
| **Kitchen gut** (cabinets, counters, backsplash, sink, fixtures) | $3,500 | $7,000 | $8,000 | Rental: paint cabinets, new counters. Flip: full demo + new shaker cabinets, granite/quartz |
| **Kitchen appliances** (range, micro, DW, fridge) | $2,000 | $3,000 | $3,500 | Builder-grade package |
| **Bathroom renovation** (water damage, tub surround, vanity, toilet, tile) | $3,500 | $5,000 | $6,000 | Must investigate ceiling staining — if mold, add $1K-$3K remediation |
| **Flooring (LVP throughout)** | $4,000 | $5,000 | $6,000 | ~1,008 sqft at $4-$6/sqft installed. Rip out carpet + vinyl |
| **Wood paneling removal + drywall** | $1,500 | $4,000 | $5,000 | Living room, kitchen, dining, hallway. Rental: paint over. Flip: hang, tape, mud, texture |
| **Popcorn ceiling removal** | $0 | $2,000 | $5,000 | Rental: leave it. Flip: scrape + retexture. **Asbestos test first** — if positive, add $2K-$3K for abatement |
| **Interior paint (full house)** | $2,000 | $2,500 | $3,000 | Walls, ceilings, trim, doors after drywall work |
| **Exterior paint + siding repair** | $2,000 | $4,000 | $5,000 | Scrape, prime, paint. Replace damaged sections near back door + new back door |
| **Electrical updates** | $1,500 | $3,000 | $4,000 | Panel upgrade for HVAC, GFCI outlets, new fixtures/switches. 1959 wiring may need partial update |
| **Plumbing repairs** | $1,000 | $2,000 | $6,000 | Fix bathroom leak source, new fixtures. Worst-case: galvanized repipe ($4K-$6K pier & beam) |
| **Windows** | $0 | $0 | $4,000 | Replace diamond-pattern + single-pane originals with vinyl. Optional for rental, recommended if budget allows |
| **Dumpster + estate cleanout** | $1,000 | $1,200 | $1,500 | Haul out all furniture/personal items + demo debris |
| **Landscaping/yard** | $300 | $800 | $1,000 | Basic cleanup, grass seed. Yard is big but low-maintenance |

---

### THREE SCENARIOS

| Scenario | Budget | What You Get |
|----------|--------|-------------|
| **Rental-Ready (minimum viable)** | **$30,000 - $38,000** | HVAC, kitchen refresh (paint cabinets, new counters, appliances), bathroom fix, LVP flooring, paint over paneling, clean up. Skip popcorn, skip windows. Gets you to $1,500-$1,650/mo rent. |
| **Mid-Range Flip** | **$45,000 - $55,000** | Full gut: HVAC, kitchen gut, bathroom gut, panel removal + drywall, popcorn removal, new flooring, all new paint, electrical updates, exterior paint. ARV: $175K-$195K. |
| **Full Worst-Case** | **$60,000 - $80,000** | Everything above PLUS: foundation work if needed ($3K-$8K), galvanized pipe repipe ($4K-$6K), asbestos abatement ($2K-$3K), roof replacement ($6K-$8K), new windows ($4K). ARV: $195K-$215K. |

---

### TOP 5 COST DRIVERS

1. **HVAC installation** ($10K-$14K) — No central system exists. Non-negotiable for any exit strategy.
2. **Kitchen gut** ($3.5K-$8K) — 100% original, nothing is salvageable for a flip.
3. **Flooring** ($4K-$6K) — Carpet and vinyl are both shot. Full house LVP.
4. **Wood paneling removal + drywall** ($1.5K-$5K) — Every common area has paneling. Must address.
5. **Bathroom + water damage** ($3.5K-$6K) — Ceiling staining means active or past leak. Mold risk.

---

### HIDDEN COST RISKS

| Risk | Probability | Cost If Found | Why Flagged |
|------|------------|---------------|-------------|
| Asbestos in popcorn ceilings | **HIGH** | $2,000-$5,000 | 1959 build. Test before scraping. |
| Galvanized pipes (need repipe) | **HIGH** | $4,000-$6,000 | 1959 build, no visible repipe evidence. Pier & beam makes repipe easier. |
| Lead paint | **MODERATE** | $1,500-$3,000 | 1959 build. Present under layers. |
| Foundation movement (pier settling) | **MODERATE** | $3,000-$8,000 | 67-year pier & beam in Dallas clay soil. Photos look OK but must verify. |
| Sewer line (clay/cast iron) | **MODERATE** | $3,000-$6,000 | Original 1959 plumbing. Camera scope recommended. |
| Termite damage (wood frame + pier & beam) | **LOW-MODERATE** | $1,500-$5,000 | Wood frame construction with soil contact at piers. |
| Mold behind bathroom walls | **MODERATE** | $1,000-$3,000 | Visible ceiling staining near shower. |
| Aluminum wiring | **LOW** | $3,000-$8,000 | 1959 is pre-aluminum era (1965-1973), but verify panel. |

---

### DEAL IMPACT (70% Rule)

```
MID-RANGE FLIP SCENARIO:
ARV:                        $195,000 (midpoint)
70% of ARV:                 $136,500
Repairs (mid-range):        -$50,000
Assignment fee:             -$10,000
───────────────────────────────────────
MAX ALLOWABLE OFFER:        $76,500

WORST-CASE SCENARIO:
ARV:                        $195,000
70% of ARV:                 $136,500
Repairs (worst-case):       -$70,000
Assignment fee:             -$10,000
───────────────────────────────────────
MAX ALLOWABLE OFFER:        $56,500

RENTAL-READY SCENARIO:
As-is value:                $145,000 (list price)
Repairs (rental-ready):     -$34,000
Monthly rent:               $1,500-$1,650
Cash flow after PITI:       Depends on purchase price + financing
```

**At the $145K list price:** This is overpriced for an investor. $100K-$115K is the target range.
- At $100K + $50K rehab = $150K all-in → $45K gross profit on $195K ARV (solid)
- At $115K + $50K rehab = $165K all-in → $30K gross profit (thin)
- Buy-and-hold at $100K + $34K rehab = $134K all-in → $1,500/mo rent (better play)

---

*Estimate based on listing photos only (24 photos reviewed via Chrome extension on Redfin). Physical inspection required before final budget. Costs based on DFW 2025-2026 contractor pricing (1.00x market factor). Hidden costs (asbestos, pipes, foundation) cannot be confirmed from photos — budget worst-case until inspected. This is not a professional inspection report.*

---

## OUTPUT FORMAT

Deliver rehab estimates as:
1. **R.E.H.A.B.+F Zone Grades** — table with 6 zones, grade (1-4), key findings per zone
2. **Photo Observations** — what the photos show, organized by exterior and interior
3. **Line-Item Rehab Budget** — every item with rental-ready, mid-range, and worst-case columns + notes
4. **Three Scenarios Summary** — rental-ready, mid-range flip, worst-case with total budgets and what each gets you
5. **Top 5 Cost Drivers** — the biggest expenses called out explicitly
6. **Hidden Cost Risks** — items photos can't confirm with probability and cost if found
7. **Deal Impact** — 70% rule MAO at mid-range and worst-case repair numbers
8. **Disclaimer** — photos only, physical inspection required, market factor noted

---

## KNOWN LIMITATIONS

| Limitation | Impact | Workaround |
|-----------|--------|------------|
| Photos can't see behind walls (pipes, wiring, mold, insulation) | Hidden costs may add $5K-$20K | Use year built + visible signals to flag risks. Always include worst-case scenario. |
| Not all listings have interior photos | Can't grade Zones A, B, H, E accurately | Note as "INTERIOR UNKNOWN" and provide range based on year/exterior only |
| Chrome extension may not load all photos in gallery | Could miss key rooms | Click through all category tabs. If gallery fails, try Zillow photos as backup. |
| Listing photos are staged to minimize problems | Photos show the BEST version of the property | Look for what's NOT shown (no bathroom photos = bathroom is bad). Look at edges, corners, ceilings. |
| Cost estimates are market averages, not contractor bids | Actual costs vary by contractor and scope discovery | Use Perplexity for local costs. Note that estimates are for budgeting, not bidding. |
| No interior video walkthrough analysis | Can't assess floor levelness, sounds, smells | Note that floor levelness, moisture, and smells require on-site visit |
| Rural markets may not have comparable cost data | Default DFW pricing may be off | Apply market adjustment factor. Note when local cost data is unavailable. |
| Redfin photo gallery layout changes over time | Chrome workflow may need adjustment | If gallery doesn't load as expected, try clicking the main hero photo to enter fullscreen mode |

---

## QUALITY CHECKLIST

Before delivering, verify:
- [ ] Were all available listing photos reviewed (not just a sample)?
- [ ] Are all 6 R.E.H.A.B.+F zones graded with evidence from photos?
- [ ] Are zones with no photo evidence marked as "UNKNOWN" (not guessed)?
- [ ] Does every line item show a range (low-high), not a single number?
- [ ] Are all three scenarios calculated (rental-ready, mid-range, worst-case)?
- [ ] Are the top 3-5 cost drivers called out explicitly?
- [ ] Are hidden cost risks flagged based on year built, foundation type, and visible signals?
- [ ] Is the deal impact calculated at both mid-range and worst-case?
- [ ] Is the market cost adjustment factor noted?
- [ ] If Property Recon was run, is that data being used (not re-researched)?
- [ ] If Comp Analyzer was run, are flip rehab costs cross-referenced (do the numbers make sense)?
- [ ] Does the estimate honestly reflect what the photos show (no inflation or deflation)?
- [ ] Is there a disclaimer that this is based on photos only?
- [ ] Would a wholesaler feel confident using these numbers to make an offer or build a buyer package?
