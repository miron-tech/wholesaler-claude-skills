# Deal Memory

This folder is where your deal data lives. Claude reads and writes here automatically.

The example deals included (3846 Happy Canyon Dr, 1122 Pinedale Ln) are real recon outputs from testing — keep them as reference so Claude knows the exact format to follow. Add your own deals alongside them or delete the examples once you're comfortable.

## Structure

```
deals/
├── pipeline.md              ← All your deals in one table
├── market-profiles/          ← Cached market data by ZIP (reusable across deals)
├── county-configs/           ← Assessor URLs, form quirks, API endpoints by county
└── [address-slug]/           ← One folder per property
    ├── recon.md              ← Full property recon report
    ├── rehab.md              ← Rehab estimate (if run)
    └── comps.md              ← Comp analysis (if run)
```
