# Wholesaler Claude Skills — by CyclSales

## What This Is
AI-powered tools for real estate wholesalers. Give Claude an address and it pulls everything you need.

## First Step On Any Task
1. Read `.claudeskills/start-here-SKILL.md` — it routes every request to the correct skill
2. Follow the skill's process and output format
3. All output follows `_system/output-format.md` formatting rules
4. All deal data follows `_system/deal-memory.md` persistence rules

## System Files
| File | Purpose |
|------|---------|
| `_system/output-format.md` | Visual design system — character palette, section order, templates |
| `_system/deal-memory.md` | Persistent state — how to read/write `./deals/` directory |

## MCP Servers Recommended

| MCP Server | What It Powers | Required? |
|------------|---------------|-----------|
| **Perplexity** | Market data, neighborhood intel, property values | Recommended |
| **Firecrawl** | Zillow/Redfin scraping, LLC lookups | Recommended |
| **Claude in Chrome** | County assessor/trustee lookups (interactive forms) | Optional |

## Available Skills

| Skill | File | Use When |
|-------|------|----------|
| Start Here | `.claudeskills/start-here-SKILL.md` | First run, routing, pipeline status, "what should I do next" |
| Property Recon | `.claudeskills/property-recon-SKILL.md` | You have an address and need the full picture — owner, value, taxes, liens, motivation score |
| Comp Analyzer | `.claudeskills/comp-analyzer-SKILL.md` | You need comps, ARV, and a defensible number to make offers or build deal packages |
| Rehab Estimator | `.claudeskills/rehab-estimator-SKILL.md` | You have a listing with photos and need repair cost estimates across 3 scenarios |
| Creative Finance Structurer | `.claudeskills/creative-finance-SKILL.md` | A cash offer doesn't work — structure a subject-to, seller finance, lease option, or wrap |
| Conversation Coach | `.claudeskills/conversation-coach-SKILL.md` | You need to know what to say to a seller based on their situation and objections |
| Deal Stacker | `.claudeskills/deal-stacker-SKILL.md` | Rank your pipeline and know which deals to focus on this week |

## Skill Chain
```
/property-recon → /comp-analyzer → /rehab-estimator → /creative-finance → /conversation-coach → /deal-stacker
```
> **Tool #7 (CyclSales Connect)** — CRM integration is available exclusively to CyclSales clients.

## Routing

The orchestrator (`start-here-SKILL.md`) handles all routing. Quick reference:

```
IF user provides an address → Check ./deals/ for existing data, then /property-recon
IF user says "rehab" or "estimate" or provides photos → /rehab-estimator
IF user says "comps" or "ARV" or "value" → /comp-analyzer
IF user says "creative finance" or "sub-to" or "seller finance" or "structure" or "wrap" or "lease option" → /creative-finance
IF user says "call" or "script" or "objection" or "what do I say" → /conversation-coach
IF user says "analyze this deal" → Chain: /property-recon → /comp-analyzer → /rehab-estimator
IF user says "pipeline" or "what should I work on" or "stack" or "rank my deals" or "which deals" → /deal-stacker
IF vague request → Show pipeline status, suggest highest-impact action
```

## Context Matrix
Skills receive ONLY the data they need from prior skills. See `start-here-SKILL.md` for the full matrix. Key rule: selective context produces better output than dumping everything.

## Deal Memory
All deal data persists in `./deals/`. See `_system/deal-memory.md` for the full protocol:
- Per-deal folders with recon, rehab, comps, photos
- Market profile cache by ZIP (reusable across deals)
- County config cache (assessor URLs, form quirks)
- Pipeline registry (append-only)
- Learnings journal (calibrates estimates over time)

## Built by CyclSales
These tools were built by [CyclSales](https://cyclsales.com) — an AI-powered CRM for real estate investors and service businesses. If you want these workflows running on autopilot with AI follow-up, pipeline management, and voice AI — check out what we built.
