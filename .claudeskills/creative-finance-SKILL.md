---
name: creative-finance
description: "Structure creative finance deals — subject-to, seller financing, lease options, wraps, and hybrids. Use when a wholesaler needs to analyze a deal that doesn't work as a traditional cash assignment. Calculates payments, cash flow, equity capture, and exit scenarios. Uses Perplexity MCP for current rate data and market context."
---

# Creative Finance Structurer Skill

> **Purpose:** When cash offers don't work, structure creative deals that let you control properties with little or no money down — and show sellers why it's a better option than listing.

---

## PREREQUISITE
- **Perplexity MCP** — for current mortgage rates, rental rates, and market data

---

## WHAT THIS SKILL DOES

- Analyzes properties for creative finance viability (subject-to, seller finance, lease option, wrap, hybrid)
- Calculates monthly payments, cash flow, equity position, and ROI
- Compares creative vs. cash offer to show which is better for the seller AND the buyer
- Produces a clear deal structure the wholesaler can present to their seller or buyer
- Models multiple exit strategies with actual numbers

## WHAT THIS SKILL DOES NOT DO

- Does not provide legal advice — creative deals NEED an attorney
- Does not guarantee loan assumptions or due-on-sale clause outcomes
- Does not replace a title search
- Does not calculate tax implications (consult a CPA)

---

## INPUTS NEEDED

| Input | Required | Example |
|-------|----------|---------|
| Property address | Yes | "1234 Oak St, Dallas, TX 75201" |
| Property details | Yes | "3/2, 1,400 sqft, built 1985" |
| Estimated value (ARV or as-is) | Yes | "As-is: $180,000 / ARV: $220,000" |
| Existing mortgage info | Yes if sub-to | "Owes $120,000 at 3.5% on a 30-year, 22 years remaining" |
| Seller's situation | Yes | "Relocating, needs to stop making payments, can't sell traditional because of condition" |
| Repairs needed | Helpful | "$25,000 in cosmetic updates" |
| Monthly rent estimate | Helpful | "$1,600/month" (or skill will research via Perplexity) |
| Your available cash | Helpful | "$5,000 for closing costs and minor repairs" |

---

## GOAL

- Primary: Structure a deal that works for everyone — seller gets relief, buyer gets cash flow, wholesaler gets paid
- Secondary: Show wholesalers that creative finance isn't scary — it's just math

---

## FRAMEWORK: The 5 Creative Structures

### Structure 1 — Subject-To (Taking Over Existing Payments)

**What it is:** You take ownership of the property while the seller's mortgage stays in place. You make their payments. They walk away.

**When it works best:**
- Seller has a low interest rate (under 5%) they'd lose by selling traditional
- Seller is behind on payments or can't afford them anymore
- Property has little equity (cash offer would be insulting)
- Seller is motivated by RELIEF, not maximum price

**The math:**

```
SUBJECT-TO ANALYSIS
═══════════════════════════════════════════
Property Value (as-is):     $[value]
Existing Mortgage Balance:  $[balance]
Interest Rate:              [rate]%
Monthly P&I Payment:        $[payment]
Monthly PITI (with tax/ins): $[total_payment]
Remaining Term:             [years] years

YOUR POSITION:
Equity captured at closing:  $[value - balance] = $[equity]
Cash to seller at closing:   $[amount] (moving costs, back payments, etc.)
Cash to close (your cost):   $[back_payments + closing + seller_cash]

RENTAL ANALYSIS:
Monthly rent:                $[rent]
Monthly PITI payment:        $[total_payment]
Monthly cash flow:           $[rent - total_payment]
Annual cash flow:            $[annual]
Cash-on-cash ROI:            [annual / cash_to_close]%

EQUITY GROWTH:
Principal paydown Year 1:    $[amount]
Appreciation (3%/yr):        $[amount]
Total Year 1 equity gain:    $[total]
5-Year equity position:      $[projected]
═══════════════════════════════════════════
```

**Due-on-sale clause disclosure:**
> The existing mortgage has a due-on-sale clause. This means the lender CAN call the loan due if they discover the transfer. In practice, lenders rarely enforce this as long as payments are current. However, the risk exists. Seller should understand this risk. Use a land trust to add a layer of privacy. Consult an attorney.

### Structure 2 — Seller Financing (The Seller IS the Bank)

**What it is:** The seller owns the property free and clear (or has small balance). They carry a note — you make payments to them instead of a bank.

**When it works best:**
- Property is free and clear (no existing mortgage)
- Seller wants monthly income (retirees, investors)
- Seller can't sell at retail due to condition
- Buyer can't qualify for traditional financing

**The math:**

```
SELLER FINANCE ANALYSIS
═══════════════════════════════════════════
Property Value (as-is):     $[value]
Purchase Price:             $[price]
Down Payment:               $[down] ([percent]%)
Financed Amount:            $[price - down]
Interest Rate:              [rate]% (negotiate — 0-6% is common)
Term:                       [years] years (balloon in [X] years typical)
Monthly Payment:            $[payment]

SELLER'S POSITION:
Cash at closing:            $[down_payment]
Monthly income:             $[payment]
Total received over term:   $[total_payments]
Total return:               $[total_received - value] above property value

YOUR POSITION:
Cash to close:              $[down + closing_costs]
Monthly payment:            $[payment]
Monthly rent:               $[rent]
Monthly cash flow:          $[rent - payment]
Annual cash flow:           $[annual]
Cash-on-cash ROI:           [annual / cash_to_close]%
Equity at purchase:         $[value - price]
═══════════════════════════════════════════
```

### Structure 3 — Lease Option (Control Without Owning)

**What it is:** You lease the property with an option to buy at a set price within a set timeframe. You can sublease for cash flow or assign the option.

**When it works best:**
- Seller is willing to wait for their price
- Property doesn't need major repairs
- You want control without taking on a mortgage
- Market is appreciating (your option price stays fixed)

**The math:**

```
LEASE OPTION ANALYSIS
═══════════════════════════════════════════
Property Value (current):   $[value]
Option Price:               $[strike_price]
Option Period:              [months] months
Monthly Lease Payment:      $[lease]
Option Consideration:       $[option_fee] (non-refundable deposit)
Rent Credits (if any):      $[credit]/month toward purchase

YOUR POSITION (Sandwich Lease):
You lease from seller at:   $[lease]/month
You sublease to tenant at:  $[sublease]/month
Monthly spread:             $[sublease - lease]
Option fee from tenant:     $[tenant_option_fee]

IF TENANT EXERCISES:
Your buy price:             $[strike_price]
Tenant buy price:           $[tenant_strike]
Profit at exercise:         $[tenant_strike - strike_price]
Plus rent spread earned:    $[total_spread]
Plus option fees earned:    $[total_options]
Total profit:               $[all_combined]

IF TENANT DOESN'T EXERCISE:
You keep:                   Option fee + all rent spread
You lose:                   Your option consideration ($[option_fee])
Net profit regardless:      $[option_fee_from_tenant + spread - your_option_fee]
═══════════════════════════════════════════
```

### Structure 4 — Wrap Mortgage (All-Inclusive Trust Deed)

**What it is:** You sell the property with seller financing, but the original mortgage stays in place underneath. Your buyer's payment covers the original mortgage plus your profit.

**When it works best:**
- You took the property subject-to and want to sell with financing
- Buyer can't get traditional lending
- You want long-term cash flow from the spread

**The math:**

```
WRAP MORTGAGE ANALYSIS
═══════════════════════════════════════════
Underlying Mortgage:
  Balance:                  $[balance]
  Rate:                     [rate]%
  Payment (P&I):            $[payment]

Your Wrap Note:
  Sale Price:               $[sale_price]
  Buyer Down Payment:       $[down]
  Wrapped Amount:           $[sale_price - down]
  Rate to Buyer:            [wrap_rate]% (higher than underlying)
  Payment from Buyer:       $[wrap_payment]

THE SPREAD:
  Buyer pays you:           $[wrap_payment]
  You pay underlying:       $[payment]
  Monthly spread:           $[wrap_payment - payment]
  Annual spread income:     $[annual_spread]

EQUITY POSITION:
  Sale price:               $[sale_price]
  Underlying balance:       $[balance]
  Net equity:               $[sale_price - balance]
  Collected at closing:     $[down]
  Remaining equity (note):  $[sale_price - down - balance]
═══════════════════════════════════════════
```

### Structure 5 — Hybrid Structures

Combine any of the above:
- **Sub-to + Wrap:** Take over payments, then sell with seller financing at a higher rate
- **Seller Finance + Option:** Get seller financing with an option to refinance within X years
- **Lease Option + Assignment:** Lock up a lease option, then assign the option to a buyer for a fee
- **Sub-to + Lease Option:** Take over payments, lease-option to a tenant-buyer

---

## PERPLEXITY MCP USAGE

**For current mortgage rates:**
```
perplexity_ask: "What are current 30-year fixed mortgage rates in [state] as of [today's date]?"
```

**For rental rate estimates:**
```
perplexity_ask: "What is the average monthly rent for a [beds]/[baths] house in [zip code] or [city, state]?"
```

**For market appreciation estimates:**
```
perplexity_ask: "What is the year-over-year home price appreciation rate in [city, state] or [zip code]?"
```

---

## DECISION LOGIC

```
IF existing mortgage has rate under 5% AND seller is motivated →
   RECOMMEND: Subject-to (capture that low rate)

IF property is free and clear AND seller wants income →
   RECOMMEND: Seller financing (they become the bank)

IF property is in good condition AND seller wants their price but can wait →
   RECOMMEND: Lease option (control without buying)

IF you already hold the property sub-to AND want to exit with cash flow →
   RECOMMEND: Wrap mortgage (sell with financing)

IF cash offer would be insultingly low due to low equity →
   RECOMMEND: Subject-to or lease option (avoid the price conversation)

IF wholesaler's buyer can't get lending →
   RECOMMEND: Wrap or seller-finance exit to end buyer

IF none of the above fit →
   RECOMMEND: Cash assignment — not every deal needs to be creative

ALWAYS → Run the numbers for at least 2 structures so the wholesaler can compare
```

---

## CONSTRAINTS

- [ ] Always include a "consult an attorney" disclaimer — creative deals have legal complexity
- [ ] Always disclose the due-on-sale risk for subject-to deals
- [ ] Never present creative finance as risk-free
- [ ] Show the numbers from BOTH seller's and buyer's perspective
- [ ] Always include a cash offer comparison so the wholesaler can see the difference
- [ ] Round payments to the nearest dollar
- [ ] Use current market rates from Perplexity, not assumptions
- [ ] If the numbers don't work for a creative structure, say so — don't force it

---

## COMPLETE EXAMPLE

### Example Input:
> Structure a deal for me: 742 Maple Ave, San Antonio, TX 78201. 3/2, 1,300 sqft. Owner owes $95,000 at 3.25% with 24 years left. Property worth $165,000 as-is. Needs $10K in cosmetic work. ARV after cosmetics: $185,000. Owner is relocating to Colorado in 30 days, can't afford to list and wait. What are my options?

### Example Output:

---

## CREATIVE FINANCE ANALYSIS: 742 Maple Ave, San Antonio, TX 78201

**Property:** 3/2 | 1,300 sqft | As-is value: $165,000 | ARV: $185,000
**Existing Mortgage:** $95,000 at 3.25%, 24 years remaining
**Monthly P&I:** ~$455 | Estimated PITI: ~$680
**Seller Situation:** Relocating in 30 days, needs relief from payments

---

### OPTION 1: SUBJECT-TO (Recommended)

```
SUBJECT-TO ANALYSIS
═══════════════════════════════════════════
Property Value (as-is):     $165,000
Existing Mortgage Balance:  $95,000
Interest Rate:              3.25% ← this is gold — today's rates are 6.5%+
Monthly PITI Payment:       $680
Remaining Term:             24 years

YOUR POSITION:
Equity captured at closing:  $70,000
Cash to seller at closing:   $3,000 (moving assistance)
Back payments owed:          $0 (seller is current)
Your total cash to close:    $5,000 (seller cash + closing + minor repairs start)

RENTAL ANALYSIS (via Perplexity):
Monthly rent (3/2 in 78201): $1,450
Monthly PITI payment:        $680
Monthly cash flow:           $770
Annual cash flow:            $9,240
Cash-on-cash ROI:            184%

EQUITY GROWTH:
Principal paydown Year 1:    ~$3,200
Appreciation (4%/yr SA):     ~$6,600
Total Year 1 equity gain:    $9,800
5-Year equity position:      ~$120,000+
═══════════════════════════════════════════
```

**Why sub-to wins here:**
- That 3.25% rate is irreplaceable — the seller would lose it listing traditional
- $770/mo cash flow on $5K invested is insane ROI
- Seller gets immediate relief from payments + $3K to help move
- You capture $70K in equity on day one

**Risk:** Due-on-sale clause. Lender could call the loan. Mitigated by: land trust transfer, keeping payments current, using a servicing company. Low probability but real risk. Attorney required.

---

### OPTION 2: CASH ASSIGNMENT (For comparison)

```
CASH ASSIGNMENT ANALYSIS
═══════════════════════════════════════════
ARV:                        $185,000
MAO (70% rule):             $129,500
Minus repairs:              -$10,000
Minus assignment fee:       -$10,000
Max offer to seller:        $109,500

Seller's mortgage:          $95,000
Seller nets:                $14,500 (after paying off mortgage)

vs. Subject-to:             Seller gets $3,000 + immediate payment relief
═══════════════════════════════════════════
```

**The pitch to the seller:**
"Maria, I have two options for you. Option A: I can pay you $109,500 cash and close in 14 days. You'd walk away with about $14,500 after paying off your mortgage. Option B: I take over your payments — you're done with the house tomorrow. I'll give you $3,000 toward your move to Colorado and you never think about this property again. Which one sounds better for your situation?"

Most relocating sellers choose Option B — they want the problem gone, not a few extra thousand dollars.

---

### OPTION 3: SUB-TO + WRAP EXIT

If you want to sell the property instead of hold it:

```
SUB-TO + WRAP ANALYSIS
═══════════════════════════════════════════
You acquire sub-to at:      $95,000 (mortgage balance)
You do $10K cosmetic rehab
Your all-in:                $108,000 ($95K + $10K repairs + $3K closing)

You sell with wrap at:       $185,000 (ARV after cosmetics)
Buyer down payment (10%):   $18,500
Wrap note amount:           $166,500
Wrap rate to buyer:         7.5%
Buyer monthly payment:      $1,280

Underlying mortgage payment: $455 (P&I at 3.25%)
Your monthly spread:        $825

TOTAL INCOME:
Collected at closing:       $18,500
Monthly spread:             $825
Annual spread:              $9,900
═══════════════════════════════════════════
```

---

### RECOMMENDATION

**Go with Subject-To, hold as a rental.**

- 3.25% money is once-in-a-generation. Don't sell this — hold it.
- $770/mo cash flow pays back your $5K investment in 7 months
- You capture $70K in equity immediately
- San Antonio is appreciating at ~4%/year
- If you ever want to exit: sell with a wrap, refi, or sell outright after seasoning

**Seller pitch angle:** Lead with payment relief, not price. She's leaving in 30 days. She wants this done, not maximized.

---

*Disclaimer: Creative real estate transactions involve legal complexity. Consult a real estate attorney before executing any subject-to, seller financing, or wrap mortgage transaction. This analysis is for educational purposes and does not constitute legal or financial advice.*

---

## OUTPUT FORMAT

Deliver deal structures as:
1. **Property & Seller Summary** — the situation at a glance
2. **Recommended Structure** — full math breakdown
3. **Alternative Structure(s)** — at least one comparison
4. **Cash Offer Comparison** — so they can see the difference
5. **Seller Pitch Script** — how to present the creative offer
6. **Risk Disclosure** — what could go wrong and how to mitigate
7. **Recommendation** — clear verdict on which structure to use and why

---

## QUALITY CHECKLIST

Before delivering, verify:
- [ ] Are all calculations accurate and showing the math?
- [ ] Did you use current market rates (via Perplexity), not assumptions?
- [ ] Is there a cash offer comparison for context?
- [ ] Is the due-on-sale risk disclosed for sub-to deals?
- [ ] Is there an attorney disclaimer?
- [ ] Does the seller pitch sound natural, not technical?
- [ ] Would a wholesaler feel confident presenting this to a seller?
