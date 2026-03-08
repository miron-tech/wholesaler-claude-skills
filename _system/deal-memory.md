# Deal Memory Protocol — Wholesaler Claude Skills

Every skill references this file to understand how to read and write
persistent deal data. This is how the system remembers properties,
caches market data, and gets smarter with every deal.

---

## Overview

Deal memory lets every wholesaler skill remember properties you've
researched, neighborhoods you've analyzed, and county sites you've
navigated. It lives in a `./deals/` directory at the project root
and accumulates as you work deals.

Skills reference this file with:
```
Read ./deals/ per _system/deal-memory.md
```

---

## The ./deals/ Directory

```
./deals/
  pipeline.md               <- Active deal registry (append-only)
  learnings.md              <- Deal outcomes and calibration data (append-only)
  market-profiles/
    {zip}.md                <- Cached neighborhood data by ZIP code
  county-configs/
    {county-state}.md       <- Assessor URLs, form fields, quirks
  {address-slug}/
    recon.md                <- Property Recon output
    rehab.md                <- Rehab Estimator output
    comps.md                <- Comp Analyzer output
    photos/                 <- Downloaded/saved property photos
    notes.md                <- User notes, follow-up log
```

### File Categories

**Per-deal files** (one directory per property):
Each property gets its own folder named with a URL-safe slug of the
address (e.g., `4973-cedar-view-rd-memphis-tn-38118/`). Skills write
their output here.

**Market cache files** (reusable across deals):
`market-profiles/{zip}.md` — neighborhood data cached by ZIP code.
When Property Recon pulls neighborhood intel for 38118, it saves
the data here. Next time any property in 38118 is researched, the
skill checks for cached data first.

**County config files** (reusable across deals):
`county-configs/{county-state}.md` — assessor/trustee URLs, form
field names, search quirks, and known limitations. Updated every
time a skill successfully navigates a county site.

**Append-only files** (never overwrite):
`pipeline.md` and `learnings.md` accumulate entries over time.
Every skill may append. No skill should ever truncate these.

---

## How Skills READ Deal Memory

### 1. Check for the directory

On every skill invocation, check whether `./deals/` exists.

- **If it exists**: proceed to step 2.
- **If it does not exist**: skip deal memory loading. Proceed as if
  first-time user. Note: "No deal history found. I'll create the
  deals directory as I work."

### 2. Load only what you need

Each skill declares what it reads from deal memory:

| Skill | Reads |
|-------|-------|
| /property-recon | market-profiles/{zip}.md, county-configs/{county}.md, pipeline.md (to check if address already researched) |
| /rehab-estimator | {address}/recon.md (property details, year built, foundation), market-profiles/{zip}.md (cost adjustment) |
| /comp-analyzer | {address}/recon.md (property details, comps), market-profiles/{zip}.md |
| /deal-stacker | pipeline.md (full pipeline), {address}/recon.md, {address}/rehab.md, {address}/comps.md |
| /creative-finance | {address}/recon.md, {address}/rehab.md, {address}/comps.md |
| /conversation-coach | {address}/recon.md, {address}/rehab.md, learnings.md |
| /start-here | ALL deal files (orchestrator needs full picture) |

### 3. Handle missing files gracefully

If a file your skill wants does not exist, do not error. Instead:
- Note what is missing in a single status line
- Ask the user for the data, OR proceed with defaults
- Suggest which skill would create the missing data

### 4. Check for existing deal data

Before running a full recon on an address, check if `./deals/{address-slug}/`
already exists. If it does:

```
  EXISTING DATA FOUND

  ├── Property Recon     ✓ ran Mar 3 (4 days ago)
  ├── Rehab Estimate     ✗ not yet run
  └── Comp Analysis      ✗ not yet run

  → "Update recon"     Re-pull with fresh data
  → "Continue"         Use existing data, run next skill
  → "Start fresh"      Delete and re-run from scratch
```

### 5. Use cached market data

Before calling Perplexity for neighborhood data, check
`./deals/market-profiles/{zip}.md`. If it exists and is less than
7 days old, use the cached version. If 7-30 days old, use it but
flag as stale. Over 30 days, re-pull.

```
IF market profile exists AND age < 7 days →
  Use as-is. Note: "Using cached market data for {zip} (X days old)"

IF market profile exists AND age 7-30 days →
  Use with flag: "Market data for {zip} is X days old — numbers may have shifted"

IF market profile exists AND age > 30 days →
  Re-pull from Perplexity. Overwrite the cache file.

IF no market profile →
  Pull from Perplexity. Create the cache file.
```

### 6. Use county configs

Before navigating a county assessor/trustee site, check
`./deals/county-configs/{county-state}.md`. If it exists, use the
saved URLs, form field names, and quirks instead of searching
from scratch.

---

## How Skills WRITE to Deal Memory

### Per-Deal Files

1. Create the deal directory: `./deals/{address-slug}/`
2. Write the skill output (recon.md, rehab.md, comps.md, etc.)
3. Append an entry to `./deals/pipeline.md`
4. Confirm: "Saved recon to ./deals/{address-slug}/recon.md"

### Market Profile Cache

After pulling neighborhood data for a ZIP code:
1. Write to `./deals/market-profiles/{zip}.md`
2. Include a `Last Updated: YYYY-MM-DD` header
3. Include the source (Perplexity, Redfin, etc.)

### County Config Cache

After successfully navigating a county site:
1. Write to `./deals/county-configs/{county-state}.md`
2. Include: assessor URL, trustee URL, form field names,
   search-by options, known quirks, date verified

Format:
```markdown
# {County}, {State} — County Site Config

Last Verified: YYYY-MM-DD

## Assessor
- URL: [assessor URL]
- Search by: Address (street number + street name separate)
- Quirks: Don't enter street suffix. Use "Cedar View" not "Cedar View Rd"
- Sections to expand: Improvement Details, Sales History, Appraisal

## Trustee / Tax Collector
- URL: [trustee URL]
- Search by: Parcel number (from assessor)
- Quirks: Select "Parcel" radio button first

## Register of Deeds
- URL: [if available]
- Search by: Parcel or owner name
- Available: [yes/no/limited]

## Code Violations
- Online portal: [yes/no]
- If no: Call [phone number]
```

---

## Pipeline Registry Format

File: `./deals/pipeline.md`

```markdown
# Deal Pipeline

> Auto-maintained by Wholesaler Claude Skills.
> New entries appended at bottom. Status updated by skills.

## Active Deals

| Address | ZIP | Motivation | MAO Range | Status | Last Updated | Notes |
|---------|-----|-----------|-----------|--------|-------------|-------|
| 4973 Cedar View Rd, Memphis TN | 38118 | 2/25 LOW | $80K-$90K | Recon done | 2026-03-03 | Owner-occupied, long-term |
| 9631 Silver Meadow Dr, Dallas TX | 75217 | 5/25 MOD | $56K-$76K | Rehab done | 2026-03-04 | No HVAC, pier & beam |

## Closed Deals

| Address | Bought | Sold/Assigned | Profit | Notes |
|---------|--------|--------------|--------|-------|
```

### Appending Rules

- New rows go at the bottom of Active Deals
- Status values: `Recon done`, `Rehab done`, `Comps done`, `Offer sent`, `Under contract`, `Closed`, `Dead`
- When a deal closes or dies, move the row to Closed Deals with outcome
- Always include the date in Last Updated

---

## Learnings Journal Format

File: `./deals/learnings.md`

```markdown
# Deal Learnings

> Auto-maintained by Wholesaler Claude Skills.
> Log deal outcomes here to calibrate future estimates.
> Newest entries at the bottom of each section.

## Estimate Accuracy
- [YYYY-MM-DD] [address] Rehab estimated $45K-$55K, actual came in at $48K. HVAC was $11K (estimated $10K-$14K). Accurate.
- [YYYY-MM-DD] [address] Rehab estimated $30K-$38K, actual was $52K. Missed: galvanized pipes needed full repipe ($8K). Update: always flag pre-1970 homes for repipe.

## Market Patterns
- [YYYY-MM-DD] [38118] Memphis 38118 — homes under $150K moving fast. 24 day DOM. Good for buy-and-hold buyers.
- [YYYY-MM-DD] [75217] Dallas 75217 — investor-heavy ZIP. Hard to get deals under 70% rule. Need to target off-market.

## County Site Notes
- [YYYY-MM-DD] [Shelby County TN] Don't enter street suffix in assessor search. "Cedar View" not "Cedar View Rd"
- [YYYY-MM-DD] [Dallas County TX] DCAD assessor loads slow — wait 5 seconds after search before screenshot

## What Worked
- [YYYY-MM-DD] [address] Skip traced via TLO, called owner, used long-term-owner angle. Got verbal at $82K.
- [YYYY-MM-DD] [address] Sent rehab estimate to cash buyer as deal package. Closed in 9 days.

## What Didn't Work
- [YYYY-MM-DD] [address] Owner-occupied with 2/25 motivation — wasted time. Don't pursue under 4/25 unless other signals.
```

### Appending Rules

- Always include date in `[YYYY-MM-DD]` format
- Always include address or ZIP for context
- Write specific, actionable observations
- Append to the correct section

---

## Feedback Collection

After a deal closes (or dies), skills should prompt for outcome data:

```
  DEAL OUTCOME

  How did this one end?

  a) Closed — assigned or bought
  b) Dead — couldn't reach seller
  c) Dead — seller wanted too much
  d) Dead — rehab was worse than estimated
  e) Still working it

  (Tell me the outcome anytime — I'll log it
  and use it to calibrate future estimates.)
```

### Processing Feedback

**If (a) "Closed":**
- Ask: "What did you buy/assign at? What was the actual rehab?"
- Log to learnings.md under Estimate Accuracy and What Worked
- Move deal to Closed Deals in pipeline.md with profit

**If (b-d) "Dead":**
- Log the reason to learnings.md under What Didn't Work
- Move deal to Closed Deals in pipeline.md with reason
- If rehab was worse than estimated, log specific items that were off

**If (e) "Still working":**
- Note it. Update pipeline status if needed.

---

## Address Slug Convention

Convert addresses to URL-safe folder names:
- Lowercase everything
- Replace spaces with hyphens
- Remove commas, periods, # signs
- Include city and state abbreviation
- Include ZIP

Examples:
- `4973 Cedar View Rd, Memphis, TN 38118` → `4973-cedar-view-rd-memphis-tn-38118`
- `9631 Silver Meadow Dr, Dallas, TX 75217` → `9631-silver-meadow-dr-dallas-tx-75217`

---

## Principles

1. **Cache aggressively.** Neighborhood data and county configs don't
   change daily. Cache them and reuse across deals in the same area.

2. **Graceful degradation.** No skill breaks because deal memory is
   missing. The system works on day one with zero history.

3. **Append, don't destroy.** Pipeline and learnings accumulate.
   Deal files are preserved even after deals close.

4. **Honest about freshness.** Always show how old cached data is.
   Stale data is still useful — just flag it.

5. **The system gets smarter.** Every closed deal with outcome data
   calibrates future estimates. The more you use it, the more
   accurate it gets for your markets.
