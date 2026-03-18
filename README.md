# AI Tools for Real Estate Wholesalers

Built by [CyclSales](https://cyclsales.com) — AI-powered CRM for real estate investors.

Give Claude an address. It pulls owner info, tax records, mortgage data, red flags, and a motivation score — all from public records, in under 2 minutes. No paid lookup tools needed.

---

## What You'll Need Before Starting

| Requirement | Cost | Why |
|-------------|------|-----|
| [Claude Code](https://claude.com/code) | $20/month (Pro plan) | This is the AI that runs the tools |
| [Perplexity API key](https://www.perplexity.ai/settings/api) | ~$5 to start (prepaid credits) | Pulls neighborhood data, comps, market context |
| [Firecrawl API key](https://www.firecrawl.dev/) | Free (500 credits, no card needed) | Scrapes Redfin, Zillow, county records |
| [Claude in Chrome extension](https://chromewebstore.google.com/detail/claude-in-chrome/agkjboahgnlkpcanbedmmbgpnlihkiij) | Free | Fills out county assessor forms automatically (optional) |

Total cost to get started: **~$25/month** (Claude Pro + a few dollars in Perplexity credits). Firecrawl and the Chrome extension are free.

---

## Step 1: Install Claude Code

Claude Code is a command-line tool that lets you talk to Claude inside your terminal. If you've never used a terminal before, don't worry — you just type and press Enter.

### On Mac

1. Press **Cmd + Space** to open Spotlight
2. Type **Terminal** and press **Enter** — a window with a blinking cursor opens
3. Copy this line, paste it in (**Cmd + V**), and press **Enter**:
   ```bash
   curl -fsSL https://claude.ai/install.sh | bash
   ```
4. Wait for it to finish — you'll see "Claude Code successfully installed!"
5. Type `claude` and press **Enter** to start it
6. It will open your browser to sign in — log in with your Claude account (you need at least a Pro plan at $20/month)
7. Once signed in, go back to your terminal — you're in Claude Code

### On Windows

1. **Install Git first** — download it from [git-scm.com/downloads/win](https://git-scm.com/downloads/win), run the installer, click "Next" on every screen
2. Press **Win + X** and select **Windows PowerShell**
3. Paste this line and press **Enter**:
   ```powershell
   irm https://claude.ai/install.ps1 | iex
   ```
4. Close PowerShell and open it again (so it recognizes the new `claude` command)
5. Type `claude` and press **Enter**
6. Sign in with your Claude account when the browser opens

### Don't have a Claude account yet?

1. Go to [claude.ai/pricing](https://claude.ai/pricing)
2. Sign up for the **Pro plan** ($20/month) — this is the minimum needed for Claude Code
3. You can use Google, Apple, or email to create your account

---

## Step 2: Get Your Perplexity API Key

Perplexity lets Claude search the internet for property data, neighborhood info, and comparable sales. This is what makes the tool pull live data instead of asking you to look things up.

**Important:** A Perplexity Pro chat subscription ($20/month) is NOT the same thing as API access. You need API credits, which are billed separately.

### How to get the key

1. Go to [perplexity.ai](https://www.perplexity.ai/) and create an account (Google sign-in is fastest)
2. Once logged in, go to [perplexity.ai/settings/api](https://www.perplexity.ai/settings/api)
   - Or click your **profile icon** (bottom-left) → **Settings** → **API** tab
3. **Add a payment method** — you need to add a credit card before you can create a key
4. **Load prepaid credits** — start with $5, that's enough for dozens of property lookups
   - Perplexity uses prepaid credits (you pay upfront, not after). When credits run out, calls stop working. $5 lasts a long time for this use case.
5. Click **"Generate New Key"**
6. Give it a name (something like "Claude Code" or "Wholesaler Tools")
7. **Copy the key immediately** — it starts with `pplx-` and looks like `pplx-abc123def456...`
   - You will NOT be able to see it again after you close this screen. If you lose it, just generate a new one.

Save this key somewhere — you'll paste it into a config file in Step 5.

---

## Step 3: Get Your Firecrawl API Key

Firecrawl lets Claude scrape websites like Redfin, Zillow, and county tax records. It reads the data off those pages so Claude can analyze it.

### Create your account

1. Go to [firecrawl.dev](https://www.firecrawl.dev/) and click **"Start for free"** (or go directly to [firecrawl.dev/signin](https://www.firecrawl.dev/signin))
2. Make sure you're on the **"Sign Up"** tab at the top — not "Log In"
3. Pick how you want to sign up:
   - **Continue with Google** — fastest, one click
   - **Continue with GitHub** — one click if you have a GitHub account
   - **Email + password** — enter your email, create a password, click "Create Account"
4. You might see a CAPTCHA checkbox — just check the box and complete it if it pops up
5. No credit card needed to create an account

### Get your API key

1. After signing up, you'll land on your **Dashboard** at [firecrawl.dev/app](https://www.firecrawl.dev/app)
2. Your API key is shown right on the main dashboard screen — look for a string that starts with `fc-`
3. If you don't see it, click **"API Keys"** in the left sidebar, or go directly to [firecrawl.dev/app/api-keys](https://www.firecrawl.dev/app/api-keys)
4. Click the **copy icon** next to the key to copy it
5. The key looks like `fc-a1b2c3d4e5f6...` — save it somewhere safe

### Free credits and adding money

**You start with 500 free credits** — no credit card needed. One credit = one page scraped. Each property lookup uses about 5-10 credits (Redfin listing, tax records, etc.), so 500 credits gets you roughly 50-100 property lookups.

When your free credits run out, you'll need to upgrade:

1. Go to [firecrawl.dev/app](https://www.firecrawl.dev/app) and click **"Upgrade"** or go to the billing section
2. Pick a plan:

   | Plan | Price | Credits/Month |
   |------|-------|---------------|
   | Free | $0 | 500 one-time (not monthly) |
   | Hobby | $9/month | 3,000/month |
   | Standard | $47/month | 100,000/month |

   The **Hobby plan at $9/month** is more than enough for most wholesalers doing 10-20 property lookups per week.

3. Enter your credit card and confirm

**Heads up:** Free credits are a one-time allotment, not monthly. Once they're gone, they're gone. There's no pay-per-use option — you have to pick a monthly plan when you need more.

Save your API key somewhere — you'll paste it into a config file in Step 5.

---

## Step 4: Install Claude in Chrome (Optional)

County assessor and tax websites use interactive forms — dropdown menus, search buttons, multi-page results. Normal web scrapers can't handle these. The Claude in Chrome extension lets Claude actually use your browser: fill in forms, click buttons, and read the results.

**Without it**, Property Recon still works — Claude will give you the county website URL and tell you what to search manually. **With it**, Claude does the county lookup automatically.

### How to install

1. Open **Google Chrome** on your computer (this only works in Chrome)
2. Go to the [Chrome Web Store listing](https://chromewebstore.google.com/detail/claude-in-chrome/agkjboahgnlkpcanbedmmbgpnlihkiij)
3. Click **"Add to Chrome"**
4. Click **"Add extension"** in the popup
5. That's it — the extension runs in the background when Claude Code needs it

No configuration needed. Claude Code detects it automatically.

---

## Step 5: Connect Your API Keys

Now you need to tell Claude Code where to find your Perplexity and Firecrawl keys. You do this by creating a config file.

### Option A: Global config (works in every project)

1. Open your terminal
2. Create the config folder and file:

   **Mac/Linux:**
   ```bash
   mkdir -p ~/.claude && touch ~/.claude/mcp.json
   ```

   **Windows (PowerShell):**
   ```powershell
   New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude" | Out-Null
   New-Item -ItemType File -Force -Path "$env:USERPROFILE\.claude\mcp.json" | Out-Null
   ```

3. Open the file in a text editor and paste this (replace the two API keys with yours):

   ```json
   {
     "mcpServers": {
       "perplexity": {
         "command": "npx",
         "args": ["-y", "@perplexity-ai/mcp-server"],
         "env": {
           "PERPLEXITY_API_KEY": "pplx-your-key-here"
         }
       },
       "firecrawl": {
         "command": "npx",
         "args": ["-y", "firecrawl-mcp"],
         "env": {
           "FIRECRAWL_API_KEY": "fc-your-key-here"
         }
       }
     }
   }
   ```

4. Save the file
5. **Fully quit and restart Claude Code** — it only reads this file on startup

### Option B: Project-only config

If you only want these tools available in one project folder, create a `.mcp.json` file in that project folder instead of `~/.claude/mcp.json`. Same contents.

### How to verify it's working

1. Start Claude Code (`claude` in your terminal)
2. Look for "perplexity" and "firecrawl" in the MCP server list when Claude starts up
3. If you don't see them, double-check your key formatting and restart Claude Code

---

## Step 6: Download the Wholesaler Tools

1. Open your terminal and navigate to wherever you want to keep projects:
   ```bash
   cd ~/Documents
   ```

2. Clone this repo:
   ```bash
   git clone https://github.com/miron-tech/wholesaler-claude-skills.git
   ```

3. Copy the tool files into your project folder:
   ```bash
   cp -r wholesaler-claude-skills/.claudeskills your-project/.claudeskills
   cp wholesaler-claude-skills/CLAUDE.md your-project/CLAUDE.md
   ```

   Replace `your-project` with whatever folder you want to work from. If you don't have a project folder yet, create one:
   ```bash
   mkdir ~/Documents/my-wholesale-deals
   cp -r wholesaler-claude-skills/.claudeskills ~/Documents/my-wholesale-deals/.claudeskills
   cp wholesaler-claude-skills/CLAUDE.md ~/Documents/my-wholesale-deals/CLAUDE.md
   ```

---

## Step 7: Use It

1. Open Claude Code inside your project folder:
   ```bash
   cd ~/Documents/my-wholesale-deals
   claude
   ```

2. Give it an address:
   ```
   Run property recon on 4821 Cedar Ln, Memphis TN 38118
   ```

3. Add any context you have to get better results:
   ```
   Run property recon on 4821 Cedar Ln, Memphis TN 38118. Looks vacant, overgrown yard, mail piling up.
   ```

Claude will pull owner info, tax records, comps, and red flags. It takes about 1-2 minutes depending on which MCP servers you have connected.

---

## What Property Recon Pulls

| Data | Source | MCP Required |
|------|--------|-------------|
| Owner name, mailing address | County assessor (via Chrome or manual) | Chrome (optional) |
| Assessed value, tax status, delinquency | County assessor + Firecrawl | Firecrawl |
| Listing data, price history, photos | Redfin/Zillow scrape | Firecrawl |
| Comparable sales | Perplexity search + Redfin | Perplexity + Firecrawl |
| Mortgage balance, equity estimate | Perplexity + county records | Perplexity |
| Liens, code violations | County records | Chrome (optional) |
| Neighborhood data (median values, DOM, crime) | Perplexity | Perplexity |
| Motivation score (0-25) | Calculated from all signals above | — |

Without any MCP servers, Claude will ask you to provide the data manually and still calculate the motivation score.

---

## Rehab Estimator

Give Claude property photos and it produces a line-item rehab estimate. Photos can come from anywhere:

- **A listing** — give Claude a Redfin or Zillow URL (or just the address, it'll find it) and it browses the photos via Chrome
- **Your own photos** — took photos on a walkthrough or drive-by? Drop them in a folder and give Claude the path
- **Google Drive** — seller or agent sent you photos? Download them to a folder and point Claude at it

**What it produces:**

- **R.E.H.A.B.+F Score** — 6-zone condition grading (Roof/Exterior, Electrical/HVAC, Hydro/Plumbing, All Interior Surfaces, Bathrooms/Kitchen, Foundation/Structure)
- **Line-item repair budget** — every item with cost ranges, not single numbers
- **Three scenarios** — rental-ready (minimum viable), mid-range flip, and full worst-case
- **Hidden cost risks** — flags items photos can't confirm (asbestos, galvanized pipes, foundation movement) based on year built
- **Deal impact** — plugs repair numbers into the 70% rule to calculate your max offer

**Examples:**
```
Run a rehab estimate on 9631 Silver Meadow Dr, Dallas TX 75217. Listed on Redfin as investor special.
```
```
Run a rehab estimate using the photos in ~/Desktop/cedar-ln-photos/
```
```
I have property photos in ~/Downloads/seller-photos/. Run a rehab estimate for 4821 Cedar Ln, Memphis TN. 3/2, 1,400 sqft, built 1972, slab foundation.
```

Works best after Property Recon — it uses the property details (year built, foundation type, HVAC type) to make smarter estimates.

**MCP needed:** Depends on your photo source. Listing photos need Chrome + Firecrawl. Local folder photos need nothing — Claude reads images directly. All sources use Perplexity for local contractor costs.

### What Rehab Estimator Analyzes

| Zone | What It Covers | MCP Required |
|------|---------------|-------------|
| Roof, Exterior & Envelope | Roof, siding, windows, doors, yard, fence | Chrome |
| Electrical & HVAC | Panel, wiring, heating, cooling, fixtures | Chrome |
| Plumbing | Water damage, mold, water heater, fixtures, pipes | Chrome |
| Interior Surfaces | Walls, ceilings, flooring, trim, doors, paint | Chrome |
| Bathrooms & Kitchen | Cabinets, counters, appliances, fixtures, tile | Chrome |
| Foundation & Structure | Foundation type, settling, subfloor, termites | Chrome |

Cost tables are calibrated for DFW with adjustment factors for Memphis, Houston, San Antonio, Phoenix, Atlanta, and other major investor markets.

---

## Comp Analyzer

Give Claude a property address and it finds comparable sales, calculates ARV, and tells you your max offer — no manual comp research needed.

**What it does:**

1. **Wide search** — uses Perplexity to find recent sales near the subject property (adjustable radius and time window)
2. **Deep scrape** — uses Firecrawl to pull detailed data from Redfin, Zillow, and county records for each comp
3. **Filter & adjust** — keeps only qualified comps (±20% sqft, ±1 bed/bath, within 1 mile, last 90 days) and applies adjustments for differences in size, features, and condition
4. **Calculate ARV** — averages adjusted $/sqft across remaining comps, cross-references with Zillow/Redfin automated estimates, assigns a confidence level
5. **Deal math** — runs the 70% rule with your repair estimate to give you a max allowable offer range

**Examples:**
```
Run comps for 4821 Cedar Ln, Memphis TN 38118. 3/2, 1,450 sqft, built 1960. Needs full rehab, estimated repairs $50K.
```
```
What's the ARV on 512 Walnut Rd, Dallas TX? It's a 4/3, 2,200 sqft. Renovated condition.
```
```
Pull comps for this deal — I need to build a package for my buyers.
```

Works best after Property Recon — it uses the property details (beds, baths, sqft, condition) to find better comps. But you can run it standalone with any address.

### What Comp Analyzer Pulls

| Data | Source | MCP Required |
|------|--------|-------------|
| Recent comparable sales (addresses, prices, details) | Perplexity search | Perplexity |
| Detailed comp data (sqft, beds, baths, sale date, DOM, condition) | Redfin/Zillow scrape | Firecrawl |
| Assessed values, last recorded sale | County assessor | Firecrawl or Chrome |
| Automated value estimates (Zestimate, Redfin Estimate) | Perplexity search | Perplexity |
| Area median price, average $/sqft, market trends | Perplexity search | Perplexity |

**Confidence levels:** The report tells you how reliable the ARV estimate is — HIGH (4+ tight comps), MODERATE (3-4 comps with some variance), LOW (1-2 comps or wide search area), VERY LOW (no good comps, zip code averages only). You always know how much to trust the number.

---

## Seller Conversation Coach

Paste any seller conversation — text threads, emails, call notes — and get the exact next message to send.

**What it does:**

1. **Reads the conversation** — identifies seller motivation level, emotional state, and objections
2. **Detects creative finance opportunities** — catches when a seller mentions their mortgage, low rate, or payments
3. **Recommends your next message** — the actual words to send, not a generic template
4. **Explains why it works** — so you learn to read sellers, not just copy-paste
5. **Predicts their response** — tells you what they'll likely say back and how to handle each scenario

**Examples:**
```
Here's my text thread with a seller on Cedar View Rd. He hasn't responded in 5 days. What should I say?
```
```
This seller keeps saying her neighbor sold for $175K. How do I handle this objection?
```
```
Probate lead — daughter inherited the house, sounds overwhelmed. What's the right approach?
```

Works best after Property Recon — it uses your intel (tax status, ownership length, Redfin estimate) to calibrate the coaching. But you can run it standalone with just the conversation.

**MCP needed:** None — this skill runs standalone. Enhanced by Property Recon and Comp Analyzer data when available.

---

## Help Us Map More Cities

Property Recon can auto-check code violations in cities that publish open data (Dallas, New Orleans, LA, San Francisco, Buffalo, Mesa AZ, Seattle — so far). For other cities, it falls back to a phone number.

**If you find a city with an open data portal for code violations** — [open an issue](https://github.com/miron-tech/wholesaler-claude-skills/issues/new?template=new-city-data-source.yml). There's a template that takes 30 seconds to fill out. We'll add it to the lookup table so it works automatically for everyone.

---

## More Tools Coming

More tools are in development:

- **Creative Finance Structurer** — structures sub-to, seller finance, wraps with full math
- **Deal Stacker** — ranks your pipeline and tells you which deals to focus on

Follow the repo for updates.

---

## Tool #7: CyclSales Connect (CRM Bridge) — Client Only

The 7th tool connects everything to your CRM. Create contacts, update pipelines, send messages, book appointments, and manage your entire deal flow — all from Claude Code, talking directly to your CyclSales CRM.

This tool is available exclusively to CyclSales clients. **[Book a demo](https://cyclsales.com)** to get access.

---

## Troubleshooting

**"command not found: claude"**
Close your terminal and open a new one. If it still doesn't work, the install may not have added Claude to your PATH. Run:
```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc && source ~/.zshrc
```

**Perplexity or Firecrawl not connecting**
- Make sure you fully quit and restarted Claude Code after adding your keys
- Make sure the JSON in your `mcp.json` file is valid (no trailing commas, matching brackets)
- Check that your Perplexity account has credits loaded

**"No MCP servers found"**
Your `mcp.json` file is either in the wrong location or has a formatting error. The file should be at `~/.claude/mcp.json` (Mac/Linux) or `C:\Users\YourName\.claude\mcp.json` (Windows).

**Chrome extension not working**
Make sure you're using Google Chrome (not Safari, Firefox, or Edge). The extension needs to be installed and enabled in Chrome's extension settings.

**Firecrawl ran out of credits**
The free tier gives you 500 one-time credits. Once they're used up, you'll need to upgrade to a paid plan at [firecrawl.dev/pricing](https://www.firecrawl.dev/pricing). Plans start at $9/month.

---

## Who This Is For

- Solo wholesalers who want Claude as a second brain for deal analysis
- Virtual wholesalers who need fast property research in unfamiliar markets
- Anyone who's tired of spending 20-45 minutes per property on manual lookups

## Who This Is NOT For

- Agents doing retail listings (these are built for off-market acquisitions)
- Anyone expecting a fully automated deal machine (you still close deals — Claude does the research)
- People who aren't willing to set up Claude Code and MCP servers (it takes about 15 minutes)

---

## Want This on Autopilot?

These tools are powerful inside Claude Code. But if you want property research, follow-up, pipeline tracking, and deal analysis running automatically — with AI handling your inbound calls and CRM doing your follow-up — that's what [CyclSales](https://cyclsales.com) does.

- AI-powered follow-up that runs 24/7 (SMS, email, voice)
- Pipeline with custom fields and stages built for wholesale
- Voice AI that handles inbound calls and qualifies sellers
- Done-for-you setup in 7-10 days

**[Book a demo](https://cyclsales.com)** and see what it looks like when these tools run on autopilot.

---

## License

MIT — use these however you want. If they help you close a deal, that's all we need.

---

Built by [Miron Briley](https://cyclsales.com) at CyclSales.
