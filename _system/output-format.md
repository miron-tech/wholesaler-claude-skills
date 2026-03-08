# Output Format Reference — Wholesaler Claude Skills

Every skill MUST follow this formatting specification. This creates a
consistent, professional experience across all tools — Property Recon,
Rehab Estimator, and everything that comes next.

---

## Design Principles

1. **Scannable in 5 seconds.** A wholesaler should get the verdict by skimming.
   Key data (motivation score, MAO, rehab total) is always in a predictable spot.

2. **Shows the work.** Every report displays what was saved, where it lives,
   and what to do next. No orphaned output.

3. **Consistent visual language.** All skills use the same character palette,
   section order, and spacing rules.

4. **Terminal-native.** Designed for monospace terminals (Claude Code). No
   markdown rendering, no HTML, no color codes. Built entirely from Unicode
   box-drawing characters and status indicators.

5. **Professional restraint.** No emoji. No exclamation marks. No filler.
   The report is the deliverable — present it and move on.

6. **Files first, output second.** The real deliverable lives on the filesystem
   (`./deals/`). The terminal output is the navigation layer.

7. **Honest about data quality.** Every data point shows its source.
   Gaps are noted, not hidden. Unknown signals stay unknown.

---

## Character Palette

These are the ONLY decorative characters used across all skills.

```
DIVIDERS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  Heavy (major sections)
──────────────────────────────────────────────────── Light (sub-sections)

BOX DRAWING
┌──────────────────────────────────────────────────┐
│  Boxed content goes here                         │
│  Used for alerts, warnings, deal verdicts        │
└──────────────────────────────────────────────────┘

TREE VIEW
├── Branch item
├── Branch item
└── Last item

NESTED TREE
├── Parent
│   ├── Child
│   └── Child
└── Parent

STATUS INDICATORS
✓   Confirmed / present / passed / saved
✗   Missing / failed / not found / denied
◑   In progress / currently pulling data
○   Available but not checked (optional)
★   High priority / recommended action

NUMBERED OPTIONS
①  ②  ③  ④  ⑤  ⑥  ⑦  ⑧  ⑨  ⑩

ACTION ARROWS
→   Points to a next step, skill, or action
```

---

## Required Output Structure

Every skill output MUST include these four sections, in this exact order.

### Section 1: Header

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  [REPORT TYPE IN CAPS]
  [Address]
  Generated [Month Day, Year]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

Rules:
- Report type in ALL CAPS (PROPERTY RECON REPORT, REHAB ESTIMATE, etc.)
- Address on its own line
- Date format: `Mar 7, 2026`
- Two-space indent before text content
- Heavy dividers top and bottom, 49 characters wide

### Section 2: Content

The actual report. Structure varies by skill. See templates below.

Rules:
- All content uses 2-space indent from the left margin
- Sub-sections separated by a single light divider (─)
- Use tree view for hierarchical data
- Keep line width at or below 55 characters

### Section 3: Files Saved

Always show what was written to disk.

```
  FILES SAVED

  ./deals/123-oak-st-dallas/recon.md       ✓
  ./deals/123-oak-st-dallas/photos/        ✓ (24 images)
  ./deals/pipeline.md                      ✓ (1 entry added)
```

Rules:
- Section label `FILES SAVED` in caps, 2-space indent
- Each file on its own line with 2-space indent
- File paths use `./` relative prefix
- Status indicator (✓) consistently spaced
- Parenthetical note when a file was updated vs created

### Section 4: What's Next

Guide the wholesaler to the logical next step.

```
  WHAT'S NEXT

  → /rehab-estimator    Estimate repairs from photos (~10 min)
  → /comp-analyzer      Pull comps and calculate ARV (~5 min)
  → /deal-stacker       Add to pipeline and prioritize (~3 min)

  Or give me another address and I'll run recon.
```

Rules:
- Always offer 2-4 concrete next steps
- Each step references a real skill with `/skill-name`
- Include time estimate in parentheses
- End with a routing fallback line

---

## Research Quality Signal

Skills that pull external data MUST show what's live vs cached vs estimated.

```
  DATA SOURCES
  ├── Redfin (Firecrawl)      ✓ live
  ├── County Assessor (Chrome) ✓ live
  ├── County Trustee (Chrome)  ✓ live
  ├── Comps (Perplexity)       ✓ live
  ├── Neighborhood (Perplexity) ✓ live
  └── Code Violations          ✗ phone only (901-636-7464)
```

When using cached data from a previous run:

```
  DATA SOURCES
  ├── Redfin (Firecrawl)      ✓ live
  ├── County Assessor          ✓ cached (2 days old)
  ├── Neighborhood (38118)     ✓ cached (2 days old)
  └── Comps (Perplexity)       ✓ live
```

Rules:
- Show after the header, before content
- ✓ for confirmed data with source
- ✗ for data that could not be obtained
- Note cached data with age
- Estimated data prefixed with ~ in the report body

---

## Template: Motivation Score Display

```
  MOTIVATION SCORE

  ┌──────────────────────────────────────────────┐
  │                                              │
  │  Score: 8 / 25         HIGH MOTIVATION       │
  │                                              │
  │  ✓ Tax delinquent           +3               │
  │  ✓ Out-of-state absentee    +3               │
  │  ✓ Long-term owner (18yr)   +2               │
  │  ✗ Vacant property          +0               │
  │  ○ Code violations          unknown          │
  │  ○ Liens                    unknown          │
  │                                              │
  └──────────────────────────────────────────────┘
```

Rules:
- Boxed for visual prominence — this is the #1 decision point
- Score and severity label on the same line
- ✓ for confirmed signals with points
- ✗ for confirmed negatives
- ○ for unchecked/unknown signals

---

## Template: Deal Verdict

```
  DEAL VERDICT

  ┌──────────────────────────────────────────────┐
  │                                              │
  │  ★ HIGH PRIORITY — Call today                │
  │                                              │
  │  Motivation:  8/25 (high)                    │
  │  MAO range:   $76,500 - $93,500              │
  │  Assignment:  $10,000 - $20,000 potential     │
  │  Risk:        Foundation needs on-site verify │
  │                                              │
  └──────────────────────────────────────────────┘
```

---

## Template: Rehab Summary

```
  THREE SCENARIOS

  ① RENTAL-READY              $30,000 - $38,000
     HVAC, kitchen refresh, LVP, paint.
     Gets you to $1,500-$1,650/mo rent.

  ──────────────────────────────────────────────

  ② MID-RANGE FLIP            $45,000 - $55,000
     Full gut: HVAC, kitchen, bath, drywall,
     popcorn, flooring, paint, electrical.
     ARV: $175K-$195K.

  ──────────────────────────────────────────────

  ③ FULL WORST-CASE       ★   $60,000 - $80,000
     Everything above + foundation, repipe,
     asbestos, roof, windows.
     ARV: $195K-$215K.
```

---

## Template: Pipeline Status

Used by the orchestrator to show active deals.

```
  DEAL PIPELINE

  ★ Hot (call today)
  ├── 4973 Cedar View Rd, Memphis     8/25  $80K-$90K MAO
  └── 1234 Oak St, Dallas             11/25 $65K MAO

  ◑ Working
  ├── 9631 Silver Meadow Dr, Dallas   5/25  Rehab estimate done
  └── 822 Pine Ave, Houston           3/25  Recon only

  ○ Cold
  └── 555 Elm St, San Antonio         2/25  Owner-occupied, no signals
```

---

## Template: Error / Data Gap

```
  ┌──────────────────────────────────────────────┐
  │                                              │
  │  ✗ COUNTY ASSESSOR UNAVAILABLE               │
  │                                              │
  │  Could not access Shelby County assessor     │
  │  site. Chrome extension may not be           │
  │  connected.                                  │
  │                                              │
  │  → Check Chrome extension is running         │
  │  → Or visit: assessormelvinburgess.com       │
  │  → Continue without assessor data            │
  │                                              │
  └──────────────────────────────────────────────┘
```

---

## Anti-Patterns

### DO NOT use markdown inside formatted output
```
WRONG:
  ## Property Details
  **Owner:** Boris Davis
  - Absentee: Yes

RIGHT:
  PROPERTY DETAILS

  Owner:     Boris Davis
  ├── Absentee     ✓ confirmed
  └── Entity       ✗ individual owner
```

### DO NOT use bullet points for structured data
```
WRONG:
  - Tax delinquent: Yes (+3)
  - Absentee: Yes (+2)

RIGHT:
  ✓ Tax delinquent           +3
  ✓ Absentee owner           +2
```

### DO NOT use chatbot preamble
```
WRONG:
  Here is your property recon report! I've
  analyzed the data and found...

RIGHT:
  (Just start with the header. The report IS
  the deliverable. No preamble needed.)
```

### DO NOT use emoji

### DO NOT omit FILES SAVED or WHAT'S NEXT

### DO NOT hide data gaps
```
WRONG:
  (silently skip code violations section)

RIGHT:
  CODE VIOLATIONS
  ✗ No online portal — call Memphis Code
    Enforcement at 901-636-7464
```

---

## Spacing Reference

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
                                                    ← blank line
..PROPERTY RECON REPORT                             ← 2-space indent
..4973 Cedar View Rd, Memphis, TN 38118             ← 2-space indent
..Generated Mar 7, 2026                             ← 2-space indent
                                                    ← blank line
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
                                                    ← blank line
..DATA SOURCES                                      ← 2-space indent
..├── Redfin (Firecrawl)      ✓ live                ← 2-space indent
..└── County Assessor         ✓ live                ← 2-space indent
                                                    ← blank line
..──────────────────────────────────────────────     ← light divider
                                                    ← blank line
..Content sections here                             ← 2-space indent
                                                    ← blank line
..FILES SAVED                                       ← 2-space indent
                                                    ← blank line
.../deals/address/recon.md.........✓                ← 2-space indent
                                                    ← blank line
..WHAT'S NEXT                                       ← 2-space indent
                                                    ← blank line
..→ /rehab-estimator   Estimate repairs (~10 min)   ← 2-space indent
```
