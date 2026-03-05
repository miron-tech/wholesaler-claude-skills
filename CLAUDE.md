# Wholesaler Claude Skills — by CyclSales

## What This Is
AI-powered tools for real estate wholesalers. Give Claude an address and it pulls everything you need.

## MCP Servers Recommended

| MCP Server | What It Powers | Required? |
|------------|---------------|-----------|
| **Perplexity** | Market data, neighborhood intel, property values | Recommended |
| **Firecrawl** | Zillow/Redfin scraping, LLC lookups | Recommended |
| **Claude in Chrome** | County assessor/trustee lookups (interactive forms) | Optional |

## How To Use
1. Clone this repo into your project directory
2. Set up Perplexity and Firecrawl MCP servers (see README for instructions)
3. Tell Claude what you need — it routes to the right tool automatically
4. Or call a tool directly: "Run property recon on 1234 Oak St, Dallas TX"

## Available Tools

| Tool | File | Use When |
|------|------|----------|
| Property Recon | `.claudeskills/property-recon-SKILL.md` | You have an address and need the full picture — owner, value, taxes, liens, motivation score |
| Rehab Estimator | `.claudeskills/rehab-estimator-SKILL.md` | You have a listing with photos and need repair cost estimates across 3 scenarios |

## Routing

```
IF user has an address and wants property intel → property-recon-SKILL.md
IF user has listing photos or a Redfin URL and needs repair estimates → rehab-estimator-SKILL.md
IF user ran Property Recon and wants to estimate repairs next → rehab-estimator-SKILL.md
```

## Built by CyclSales
These tools were built by [CyclSales](https://cyclsales.com) — an AI-powered CRM for real estate investors and service businesses. If you want these workflows running on autopilot with AI follow-up, pipeline management, and voice AI — check out what we built.
