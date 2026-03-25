---
name: deal-stacker
description: "Rank and prioritize your wholesale deal pipeline. Use when a wholesaler has multiple leads or deals in progress and needs to know which ones to focus on this week. Scores deals across 5 dimensions, classifies exit strategies (cash assignment vs creative finance), verifies ARV gaps, and produces a ranked priority list with specific action items. Uses Perplexity MCP for market data, Firecrawl MCP + Redfin for comp verification when ARV is unverified or spread is thin."
---

# Deal Stacker / Pipeline Prioritizer Skill

> **Purpose:** Stop chasing 20 leads equally. Stack-rank your pipeline so you spend this week on the 3 deals most likely to close and pay you.

---

## PREREQUISITES
- **Perplexity MCP** — market data, rental rates, DOM, appreciation trends
- **Firecrawl MCP** — Redfin comp verification when ARV is unverified (optional but recommended)

---

## WHAT THIS SKILL DOES

- Takes your entire pipeline (3, 10, or 50 leads) and ranks them by close probability
- Scores each deal across 5 dimensions: profit potential, seller motivation, deal certainty, speed to close, and effort required
- **Classifies each deal by exit strategy** — cash assignment, sub-to, seller finance, lease option, or JV
- **Verifies ARV** when the stated number seems off or the spread is thin (routes to Comp Analyzer or fires Perplexity/Firecrawl queries)
- Identifies your TOP 3 deals to focus on this week
- Flags stale leads that need action or removal
- Flags deals that are about to expire or die if you don't act
- Recommends the ONE next action per deal that moves it forward
- **Recommends exit strategy per deal** — not just "work this deal" but HOW to monetize it
- **Builds schedule starting from today** — not a generic Mon-Fri template

## WHAT THIS SKILL DOES NOT DO

- Does not manage your pipeline for you (you update the data)
- Does not pull leads from a CRM automatically (unless CyclSales Connect skill is chained)
- Does not guarantee which deals will close — it's a prioritization framework, not a crystal ball

---

## SKILL CHAIN INTEGRATION

Deal Stacker is skill #6 in the chain. If previous skills have already been run on a deal, USE THAT DATA — don't re-research from scratch.

### Data That Flows In

| If This Skill Was Run | Data Available | How to Use It |
|-----------------------|---------------|---------------|
| Property Recon | Owner info, tax status, liens, occupancy, county data | Pre-populates seller situation, flags title issues |
| Auto-Comp Analyzer | ARV, as-is value, comp details, flip activity, confidence level | Use as verified ARV (skip Perplexity ARV query). Flag if confidence was LOW. |
| Creative Finance Structurer | Sub-to terms, cash flow, equity capture, ROI, walk-away thresholds | Use for SPREAD scoring on creative deals. Already has rental rates + reserves. |
| Conversation Coach | Seller motivation assessment, recommended approach, creative triggers detected | Use for TEMPERATURE scoring. If Coach flagged creative opportunity, classify deal accordingly. |

### Rules for Using Chain Data
```
IF Comp Analyzer was run AND confidence is MODERATE or HIGH → Use that ARV, mark as "verified"
IF Comp Analyzer was run AND confidence is LOW → Flag ARV as uncertain, run verification query
IF Creative Finance was run → Classify as creative deal, use its SPREAD metrics (equity + cash flow)
IF Conversation Coach was run → Use its motivation score for TEMPERATURE dimension
IF no previous skills were run → Enrich with Perplexity, score with available data, note gaps
```

---

## INPUTS NEEDED

| Input | Required | Example |
|-------|----------|---------|
| Deal list | Yes | See format below |
| Your goal this month | Helpful | "Need to close 2 deals" or "Need $20K in assignment fees" |
| Hours available this week | Helpful | "I can work 20 hours on acquisitions this week" |
| Previous skill data | Helpful | "I already ran comps on Deal 1" or provide the output |

**Deal format (per deal):**
```
Address: 1234 Oak St, Dallas TX
Status: [Negotiating / Offer submitted / Under contract / Cold lead / New lead]
Lead source: [Cold call / Direct mail / D4D / Referral / Inbound]
Seller situation: [Brief — "probate, out of state" or "pre-foreclosure, 60 days"]
Asking price: $X (or "hasn't named a price")
Your offer: $X (or "haven't offered yet")
ARV estimate: $X (and whether it's verified with comps or just a guess)
Repair estimate: $X
Mortgage info: [If known — balance, rate, monthly payment]
Assignment fee target: $X
Days in pipeline: X days
Last contact: [date or "3 days ago"]
Notes: [Anything relevant — "seller's daughter is decision maker," "needs to close by March 15"]
```

You can provide as little or as much detail as you have. The more data, the better the ranking.

---

## DEAL TYPE CLASSIFICATION

Before scoring, classify each deal's exit strategy. This changes how SPREAD is calculated.

| Deal Type | When to Use | Profit Metric | Timeline |
|-----------|-------------|---------------|----------|
| **Cash Assignment** | Standard wholesale. Seller sells cheap, you assign to cash buyer. | Assignment fee ($5K-$25K+) | 2-6 weeks |
| **Sub-To** | Seller has low-rate mortgage (<5%), equity available, property rents well | Equity capture + monthly cash flow | 30-60 days to close, hold long-term |
| **Seller Finance** | Seller owns free & clear or nearly, willing to carry a note | Monthly cash flow + spread on wrap/resale | 30-90 days to close |
| **Lease Option** | Seller wants higher price, you need time. Tenant-buyer exit. | Option fee + monthly spread + backend profit | 60-90 days setup |
| **JV / Co-Wholesale** | You have the deal but no buyer, or deal is in another market | Split fee (50% of assignment) | Depends on JV partner |

### Auto-Classification Rules
```
IF seller mentions "I refinanced" or "I still owe" + rate < 5% → Flag as SUB-TO candidate
IF seller owns free & clear + property rents well → Flag as SELLER FINANCE candidate
IF seller wants above-market price + you can control with lease → Flag as LEASE OPTION candidate
IF under contract + no buyer + deadline approaching → Flag as JV candidate
IF spread is $5K+ on cash assignment → Keep as CASH ASSIGNMENT
IF spread is under $5K on cash + seller has mortgage info → Explore CREATIVE before killing the deal
DEFAULT → CASH ASSIGNMENT
```

---

## FRAMEWORK: The STACK Score

Each deal gets a score from 0-25 across 5 dimensions.

### Dimension 1 — SPREAD (Profit Potential) — 0 to 5 points

**For Cash Assignment deals:**

| Assignment Fee Estimate | Score |
|------------------------|-------|
| $20,000+ | 5 |
| $15,000-$19,999 | 4 |
| $10,000-$14,999 | 3 |
| $5,000-$9,999 | 2 |
| Under $5,000 | 1 |
| Unknown / can't estimate yet | 2 (neutral) |

**For Creative Finance deals (sub-to, seller finance, lease option):**

| Profit Metric | Score |
|--------------|-------|
| Equity capture $75K+ OR cash flow $500+/mo | 5 |
| Equity capture $50-74K OR cash flow $350-499/mo | 4 |
| Equity capture $25-49K OR cash flow $200-349/mo | 3 |
| Equity capture $10-24K OR cash flow $100-199/mo | 2 |
| Equity capture under $10K AND cash flow under $100/mo | 1 |
| Unknown — need mortgage info from seller | 2 (neutral) |

> **Creative deals with BOTH equity AND cash flow:** If a deal has $50K+ equity AND $300+/mo cash flow, score it 5 regardless.

### Dimension 2 — TEMPERATURE (Seller Motivation) — 0 to 5 points

| Motivation Signal | Score |
|------------------|-------|
| Seller verbally agreed to terms / ready to sign | 5 |
| Seller named a price in your range / engaging actively | 4 |
| Seller is motivated (life event, financial pressure) but hasn't committed | 3 |
| Seller is interested but exploring options / slow responses | 2 |
| Seller is cold, unresponsive, or "just looking" | 1 |
| New lead, haven't spoken yet | 2 (neutral) |

### Dimension 3 — ABILITY (Deal Certainty) — 0 to 5 points

| Certainty Factor | Score |
|-----------------|-------|
| Under contract, title clear, buyer lined up | 5 |
| Under contract, searching for buyer | 4 |
| Offer accepted verbally, contract pending | 3 |
| In negotiation, numbers are close | 2 |
| Early conversations, big gaps in price/terms | 1 |
| Unknown — just started working the lead | 1 |

### Dimension 4 — CLOCK (Speed to Close) — 0 to 5 points

| Timeline | Score |
|----------|-------|
| Can close within 7 days | 5 |
| Can close within 14 days | 4 |
| Can close within 30 days | 3 |
| 30-60 days | 2 |
| 60+ days or no clear timeline | 1 |

### Dimension 5 — KICK (Low Effort Required) — 0 to 5 points

| Effort to Next Milestone | Score |
|-------------------------|-------|
| One phone call or one document away from closing | 5 |
| Need 1-2 more conversations to lock it up | 4 |
| Need property access, inspection, or contractor bid | 3 |
| Need significant negotiation or relationship building | 2 |
| Major obstacles — title issues, family disputes, legal complications | 1 |

### STACK Score Calculation

```
STACK Score = Spread + Temperature + Ability + Clock + Kick
Maximum: 25 points
```

**Interpretation:**

| Score | Priority | Action |
|-------|----------|--------|
| 20-25 | **CLOSE THIS WEEK** | Drop everything. This deal is hot. |
| 15-19 | **ACTIVE — high priority** | Dedicated time daily until it moves forward |
| 10-14 | **WARM — work it** | Schedule follow-ups, keep momentum |
| 5-9 | **NURTURE** | Weekly check-in, don't spend hours here |
| 0-4 | **REVIEW** | Is this deal alive? Either re-engage or remove |

---

## ARV VERIFICATION

**When to verify ARV:**
```
IF ARV is a guess (not from Comp Analyzer or MLS) → VERIFY
IF spread is thin (assignment fee < $8K) → VERIFY (small ARV error kills the deal)
IF deal scores 15+ but ARV is unverified → VERIFY (don't prioritize on bad data)
IF market is declining or slow (DOM > 60) → VERIFY (stale comps inflate ARV)
```

**How to verify (in order of reliability):**

1. **Best: Run Comp Analyzer skill** — gets 5-8 Redfin comps with flip detection
2. **Good: Firecrawl + Redfin subject page** — one scrape gives nearby sold comps
3. **Acceptable: Perplexity query** — returns market-level data, 1-2 comps max
4. **Minimum: Perplexity + Zillow/Redfin estimate check** — at least cross-reference the number

**Perplexity ARV query (when Comp Analyzer isn't being run):**
```
perplexity_ask: "Recent sold prices for [beds]/[baths] houses near [address] in [zip code] in the last 6 months. What is the median sale price and average price per square foot for [zip]?"
```

**When Perplexity ARV conflicts with stated ARV:**
- If Perplexity is 15%+ higher → Note as "ARV may be conservative, verify with comps"
- If Perplexity is 15%+ lower → Flag as "ARV RISK — market data suggests lower value. Run Comp Analyzer."
- Score SPREAD using the LOWER number until verified

---

## DISPO DIFFICULTY ASSESSMENT

For deals under contract that need a buyer, estimate how hard dispo will be:

| Factor | Easy Dispo | Hard Dispo |
|--------|-----------|------------|
| DOM in that ZIP | Under 30 days | Over 60 days |
| Price point | Under $150K (investor sweet spot) | Over $250K (smaller buyer pool) |
| Assignment fee | Under $10K (easy for buyer to absorb) | Over $20K (buyers push back) |
| Property type | SFR 3/2, good neighborhood | Odd layout, bad area, commercial zoning |
| Buyer list depth | 50+ active buyers in that market | Under 10 buyers, or new market |

**Use dispo difficulty to adjust CLOCK score:**
```
IF under contract + no buyer + hard dispo factors → Reduce CLOCK by 1 point
IF under contract + no buyer + contract expires < 14 days + hard dispo → Flag as CRITICAL
```

---

## PIPELINE HEALTH FLAGS

After scoring all deals, flag these conditions:

```
⚠️ EXPIRING: Any deal under contract with closing date within 14 days
⚠️ STALE: Any lead with no contact in 7+ days
⚠️ DYING: Any negotiation stuck at the same stage for 14+ days
⚠️ OVERLOADED: More than 5 deals scoring 15+ (you can't work them all — delegate or JV)
⚠️ EMPTY TOP: No deals scoring above 15 (pipeline problem — need more leads)
⚠️ BOTTOM HEAVY: Most deals scoring under 10 (quality problem — leads aren't motivated enough)
⚠️ THIN SPREAD: Deal under contract but assignment fee < $5K (barely worth closing)
⚠️ ARV UNVERIFIED: Deal scores 15+ but ARV is a guess (don't prioritize on bad numbers)
⚠️ LEAD DROUGHT: Fewer than 5 total deals in pipeline (stop working deals, go find more leads)
⚠️ NO CREATIVE: High-motivation seller + thin cash spread + mortgage info available = missed creative opportunity
```

---

## TOOL ROUTING

| Data Needed | Tool | Query |
|-------------|------|-------|
| ARV estimate / market median | Perplexity | "Recent sold prices for [beds]/[baths] houses near [address] in [zip] in last 6 months" |
| Verified comps (when ARV is questionable) | Comp Analyzer skill OR Firecrawl + Redfin | Run full comp analysis on that address |
| Rental rates (for creative deals) | Perplexity | "Average monthly rent for [beds]/[baths] house in [zip]" — always use ZIP-level, not citywide |
| Market speed / DOM | Perplexity | "Average days on market for homes in [zip] and current inventory trends" |
| Mortgage rates (for sub-to comparison) | Perplexity | "Current 30-year fixed mortgage rate in [state]" |
| Seller conversation history | CyclSales Connect | Pull from CRM if available |

### Parallel Execution Plan

**Batch 1 — Fire all Perplexity enrichment queries in parallel:**
- One query per deal that has missing data (ARV, rental rates, market speed)
- Group: all market context queries (DOM, appreciation, rates) for the metro area

**Batch 2 — Score all deals with enriched data**

**Batch 3 — Verify ARV on any deal where spread is thin or ARV seems off:**
- Fire Firecrawl + Redfin scrape, or recommend running Comp Analyzer

---

## DECISION LOGIC

```
IF all deals score under 10 →
  Pipeline is weak. STOP working deals. Spend 80% of time on lead gen.
  Say it directly: "Your pipeline is empty. None of these deals are likely to close this week.
  You need 20+ new leads before you'll have 2-3 closeable deals."

IF 1-2 deals score 20+ →
  These are the ONLY priority. Everything else waits.

IF 3+ deals score 15+ →
  Wholesaler needs to delegate, JV, or choose. Recommend top 3 only.

IF a deal has been in pipeline 30+ days with no progress →
  Flag for removal or one final re-engagement attempt.

IF a deal is under contract with no buyer →
  URGENT — focus dispo effort here. Estimate dispo difficulty.

IF a deal has high SPREAD but low TEMPERATURE →
  Worth a re-engagement push (the money is there if the seller cooperates).

IF a deal has high TEMPERATURE but low SPREAD (cash) →
  Check for creative finance opportunity. Ask: "Do you know what the seller owes?"
  If mortgage info available → Run Creative Finance Structurer.
  If free & clear → Explore seller finance.
  If no mortgage info → Coach wholesaler to ask (use Conversation Coach trigger phrases).

IF a deal under contract has thin spread (<$5K) + ARV unverified →
  VERIFY ARV IMMEDIATELY. A $20K ARV error is the difference between $3K and $17K fee.

IF a deal is classified as creative BUT no mortgage info yet →
  Next action is ALWAYS "get mortgage details from seller" — nothing else matters until you have the numbers.

DEFAULT →
  Rank by STACK score, present top 3, recommend exit strategy + one action per deal.
```

---

## EXIT STRATEGY RECOMMENDATION

For each deal in the top 3, explicitly recommend an exit strategy:

| Situation | Recommended Exit | Why |
|-----------|-----------------|-----|
| Spread $10K+ on cash, buyer pool is active | **Cash Assignment** | Simple, fast, proven |
| Spread < $5K on cash, seller has low-rate mortgage | **Sub-To** | Thin cash spread, but creative unlocks $50K+ equity + cash flow |
| Spread < $5K on cash, seller owns free & clear | **Seller Finance** | No equity capture on sub-to, but can wrap or resell the note |
| Seller wants above-market price, you can't make cash work | **Lease Option** | Control the property, find tenant-buyer, collect option fee + spread |
| Under contract, no buyer, deadline approaching | **JV / Co-Wholesale** | Split the fee. 50% of something beats 100% of nothing. |
| Under contract, solid spread, active buyer market | **Cash Assignment** | Don't overcomplicate it. Blast and close. |
| High motivation + low spread + creative triggers in convo | **Run Creative Finance skill** | The deal is there — just not as a cash assignment. |

---

## CONSTRAINTS

- [ ] Never rank more than 3 deals as "top priority" — the whole point is focus
- [ ] Always include the ONE next action per deal — no vague advice
- [ ] Always include the recommended EXIT STRATEGY per deal — "work this deal" isn't enough, say HOW
- [ ] Flag deals that should be killed — wholesalers hold onto dead leads too long
- [ ] If a deal is under contract with a deadline, it's always top priority regardless of score
- [ ] Present the full ranked list but make the top 3 visually obvious
- [ ] If data is missing, score it neutral (2-3) and note the gap, don't guess high
- [ ] If ARV is unverified and the deal scores 15+, flag it — don't let the wholesaler prioritize on bad numbers
- [ ] If pipeline has fewer than 5 deals, explicitly tell the wholesaler to find more leads
- [ ] Build the weekly schedule starting from TODAY'S actual day, not a generic Monday

---

## COMPLETE EXAMPLE

### Example Input:

> Goal: close 2 deals this month. 25 hours/week for acquisitions.
>
> 1. **1456 Dearing Rd, Memphis 38117** — Seller agreed to $95K yesterday. Free and clear. 4/2, ARV ~$210K (my guess). Repairs $30K. Divorcing, wants to close in 2 weeks. Haven't sent contract yet.
>
> 2. **2847 Bartlett Blvd, Memphis 38134** — Under contract at $72K. ARV $165K. Repairs $40K. Probate lead. Contract expires in 10 days. No buyer yet. Under contract 18 days.
>
> 3. **4973 Cedar View Rd, Memphis 38118** — Negotiating via text. D4D lead. Boris, 72, daughter in Atlanta. Mentioned he refinanced a few years back. Sub-to opportunity. Previous skills run: Property Recon, Comp Analyzer (ARV $180K, MODERATE confidence), Creative Finance ($65K owed at 3.25%, sub-to recommended, $503/mo cash flow, $90K equity), Conversation Coach (motivation MODERATE leaning HIGH).
>
> 4. **891 Getwell Rd, Memphis 38111** — New lead, cold call. 3/1, needs work. Owner said "I need at least $120K." Called 3 days ago.
>
> 5. **5520 Raleigh Lagrange, Memphis 38134** — D4D lead. Left door knocker 3 weeks ago. Owner texted "not interested right now" 12 days ago. Vacant looking. No numbers.

### Example Output:

---

## DEAL STACK: Week of March 3, 2026 (Starting Monday)
**Goal:** 2 deals this month | **Available:** 25 hrs/week

---

### RANKED PIPELINE

| Rank | Address | STACK | S | T | A | C | K | Type | Priority |
|------|---------|-------|---|---|---|---|---|------|----------|
| **1** | **1456 Dearing Rd** | **22** | 5 | 5 | 3 | 4 | 5 | Cash Assignment | **CLOSE THIS WEEK** |
| **2** | **2847 Bartlett Blvd** | **18** | 1⚠️ | 5 | 4 | 4 | 4 | Cash (verify ARV) | **URGENT — DISPO** |
| **3** | **4973 Cedar View Rd** | **14** | 5★ | 3 | 2 | 2 | 2 | Sub-To | **WARM — CREATIVE** |
| 4 | 891 Getwell Rd | 8 | 1 | 3 | 1 | 1 | 2 | Dead at $120K | NURTURE |
| 5 | 5520 Raleigh Lagrange | 6 | 2 | 1 | 1 | 1 | 1 | Unknown | REVIEW |

★ Creative deal — SPREAD scored on equity capture ($90K) + cash flow ($503/mo), not assignment fee
⚠️ Thin spread at stated ARV ($3.5K fee). Perplexity data suggests ARV may be $185K ($17.5K fee). VERIFY.

---

### YOUR TOP 3 THIS WEEK

---

**#1 — 1456 Dearing Rd, Memphis 38117 (STACK: 22/25)**
**Exit strategy: CASH ASSIGNMENT** | **Verbal agreement at $95K** | **Free & clear, divorcing, wants fast close**

The numbers:
- ARV: $210K (⚠️ UNVERIFIED — your guess) | 70% = $147K | Minus $30K repairs = $117K max to buyer
- Your price: $95K | Spread: $22K | Assignment fee: **$18-20K**

Market check: 38117 median listing $300K, but Colonial Acres neighborhood median SALE is $190K (down 5% YoY). 93 DOM avg — slow market. If real ARV is $190K: 70% = $133K - $30K = $103K. Spread: $8K. Still works but much thinner.

⚠️ **She agreed yesterday. Send the contract TODAY. Divorce sellers talk to lawyers and family — every day you wait, someone tells her to list it.**

⚠️ **ARV UNVERIFIED.** Run Comp Analyzer on this property. At $95K contract price you have cushion, but know your real number before pricing to buyers.

**ONE ACTION:** Send the purchase agreement today. Call to walk her through it. Get a signature in 48 hours. THEN run comps to verify ARV before you dispo.

---

**#2 — 2847 Bartlett Blvd, Memphis 38134 (STACK: 18/25)**
**Exit strategy: CASH ASSIGNMENT (verify ARV) or JV if spread is thin** | **Under contract at $72K, probate**

⚠️ **CONTRACT EXPIRES IN 10 DAYS. NO BUYER.**

Two scenarios based on ARV:

**If ARV = $165K (your number):**
- 70% = $115.5K - $40K repairs = $75.5K max to buyer
- Your price: $72K | Spread: **$3.5K** — barely worth closing
- Dispo price to buyer: $74-75K

**If ARV = $185K (Perplexity data — renovated 3/2s in 38134 avg $182K, 28 DOM):**
- 70% = $129.5K - $40K repairs = $89.5K max to buyer
- Your price: $72K | Spread: **$17.5K**
- Dispo price to buyer: $82-85K

**Dispo difficulty:** 38134 is 28 DOM, under $100K price point, active investor market = EASY dispo IF the ARV supports it.

**Decision tree:**
- If ARV confirms $180K+ → Blast at $82K, you'll find a buyer in days
- If ARV is really $165K → $3.5K fee. Either renegotiate to $60K, JV with another wholesaler for split, or let the contract expire. Don't close a deal for $2K net.

**ONE ACTION:** Pull 3 confirmed sold comps within 0.5 miles TODAY. Check Redfin for the property page — the nearby sold section will show you what renovated 3/2s are going for. If ARV is $180K+, blast immediately. If $165K, call the probate contact and ask for a 14-day extension at $65K.

---

**#3 — 4973 Cedar View Rd, Memphis 38118 (STACK: 14/25)**
**Exit strategy: SUB-TO** | **Negotiating via text, Boris (72), daughter in Atlanta**

*Previous skill data available: Property Recon ✅, Comp Analyzer ✅, Creative Finance ✅, Conversation Coach ✅*

The numbers (verified by Comp Analyzer + Creative Finance):
- ARV: $180K (MODERATE confidence) | As-is: $109-140K | Redfin estimate: $159K
- Boris owes: $65K at 3.25% (2021 refi), ~25 years remaining
- PITI: ~$450/mo | Market rent (38118): $1,095-$1,200/mo
- Net cash flow (with 15% reserves): **$503/mo**
- Equity capture at takeover: **~$90K**
- Cash-on-cash ROI: **86%**

Why not ranked higher: Boris hasn't committed. Motivation is MODERATE leaning HIGH (Conversation Coach assessment). No price or terms discussed yet. You're in rapport-building phase — 2-3 more conversations before introducing sub-to.

⚠️ Memphis declining 2.5% YoY. This is a cash flow play, not appreciation. $503/mo makes it worth it.

**ONE ACTION:** Text Boris this week. Don't pitch the deal. Ask about his daughter. Goal: get him on a phone call. Once on the phone, use Conversation Coach's "What would it look like if..." framework to plant the sub-to seed.

---

### OTHER DEALS

**891 Getwell Rd, Memphis 38111 (STACK: 8) — REALITY CHECK**
3/1, needs work. Owner wants $120K. Perplexity shows 3/1s in 38111 sell for $110-130K in average condition. After full rehab: ~$140K max.
- $140K × 70% = $98K - repairs (~$25K) = $73K max offer. Owner wants $120K. **Gap: $47K. Dead as a cash deal.**
- Creative option: If she owns free & clear, seller finance could work. But you don't know yet.
- **Action:** One more call. Ask about mortgage and property condition. If free & clear, explore seller finance. If she owes near $120K, walk away. Don't spend more than 30 minutes here this week.

**5520 Raleigh Lagrange, Memphis 38134 (STACK: 6) — PROBABLY DEAD**
D4D lead. "Not interested right now" 12 days ago. No numbers, no relationship.
- **Action:** One final text: "Totally understand. If anything changes down the road, I'm here." If no response in 7 days, move to 90-day drip list. Don't spend time here.

---

### PIPELINE HEALTH

| Flag | Status |
|------|--------|
| ⚠️ EXPIRING | Bartlett Blvd — 10 days, no buyer |
| ⚠️ THIN SPREAD | Bartlett Blvd — only $3.5K at stated ARV |
| ⚠️ ARV UNVERIFIED | Dearing Rd ($210K guess) + Bartlett Blvd ($165K vs $185K market data) |
| ⚠️ STALE | Raleigh Lagrange — 12 days no contact |
| ⚠️ LEAD DROUGHT | Only 5 deals total. Need 8-10 for consistent closings. |
| Active deals (15+) | 2 — need 3-4 |
| Creative deals in progress | 1 (Cedar View sub-to — all analysis done, execution phase) |
| Dead/dying | 1-2 (Raleigh Lagrange definitely, Getwell probably) |
| Pipeline value | $40-55K assignment fees + $90K equity capture (Cedar View) |
| Goal progress | ON TRACK if Dearing closes + Bartlett finds a buyer. Thin margin for error. |

---

### THIS WEEK'S SCHEDULE (Starting Monday March 3)

| Day | Deal | Action | Est. Time |
|-----|------|--------|-----------|
| **Mon** | #1 Dearing Rd | Send purchase agreement. Call seller to walk through it. | 2 hrs |
| **Mon** | #2 Bartlett Blvd | Verify ARV — check Redfin comps. If $180K+, build dispo blast. | 2 hrs |
| **Tue** | #1 Dearing Rd | Follow up on contract. Signed? Questions? | 1 hr |
| **Tue** | #2 Bartlett Blvd | Send dispo blast — VIP top 5 buyers + mass list + SMS | 2 hrs |
| **Wed** | #3 Cedar View | Text Boris. Ask about daughter. Keep rapport going. | 30 min |
| **Wed** | #2 Bartlett Blvd | Follow up buyer responses. Schedule property showings. | 2 hrs |
| **Wed** | #4 Getwell Rd | One discovery call. Mortgage? Condition? Timeline? | 30 min |
| **Thu** | #1 Dearing Rd | Contract should be signed. Start dispo prep if yes. | 1 hr |
| **Thu** | #2 Bartlett Blvd | No buyer? Drop price $3-5K or start JV outreach. | 1 hr |
| **Thu** | #5 Raleigh Lagrange | Final text. No response in 7 days = drip list. | 15 min |
| **Fri** | #3 Cedar View | If Boris responded, try to schedule phone call for next week. | 30 min |
| **Fri** | Pipeline | Review all 5 deals. Update status. Re-stack for next week. | 1 hr |
| | | **Total: ~14 hrs** (leaves 11 hrs for lead gen — **you need it**) | |

---

## OUTPUT FORMAT

Deliver pipeline analysis as:
1. **Ranked pipeline table** — all deals with STACK scores + deal type + priority
2. **Top 3 deep dive** — numbers, exit strategy, ARV verification status, and ONE action per deal
3. **Other deals** — brief status, exit strategy recommendation, and action for non-priority deals
4. **Pipeline health** — all applicable flags + metrics
5. **Weekly schedule** — day-by-day action plan starting from TODAY (not a generic template)

---

## QUALITY CHECKLIST

Before delivering, verify:
- [ ] Are STACK scores calculated correctly across all 5 dimensions?
- [ ] Is each deal classified by exit strategy (cash assignment, sub-to, seller finance, lease option, JV)?
- [ ] Are creative deals scored on equity + cash flow (not assignment fee)?
- [ ] Are ARVs flagged when unverified, especially on deals scoring 15+?
- [ ] Are the top 3 clearly identified with specific next actions?
- [ ] Does each top 3 deal have an explicit exit strategy recommendation?
- [ ] Are stale/dying/expiring deals flagged with urgency?
- [ ] Is dispo difficulty assessed for any deal under contract with no buyer?
- [ ] If pipeline has <5 deals, is the lead gen warning prominent?
- [ ] Is the weekly schedule realistic for the wholesaler's available hours?
- [ ] Does the weekly schedule start from TODAY, not a generic Monday?
- [ ] Are dead leads honestly identified (not propped up with false hope)?
- [ ] If previous skills were run on any deal, is that data being used (not re-queried)?
- [ ] Would a wholesaler open this and immediately know what to do today?

---

## KNOWN LIMITATIONS

| Limitation | Workaround |
|-----------|------------|
| Perplexity returns 0-2 actual comps for ARV queries | Use Comp Analyzer skill or Firecrawl + Redfin for hard comp data |
| Can't auto-pull pipeline from CRM | User provides deals manually, or chain with CyclSales Connect if configured |
| Creative finance scoring requires mortgage info | If unknown, coach wholesaler to ask seller (Conversation Coach has trigger phrases) |
| Market data (DOM, appreciation) is metro-level from Perplexity | ZIP-level data is more accurate — always ask for ZIP-specific stats |
| Dispo difficulty is estimated, not measured | Would need buyer list data and historical close rates to be precise |
| Schedule assumes wholesaler works weekdays | Adjust if they say otherwise |
