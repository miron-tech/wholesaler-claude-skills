# Deal Memory

This folder is where your deal data lives. Claude reads and writes here automatically.

Your deals go here. Each property gets its own folder, created the first time you run a recon on it. The worked examples inside each skill file show the exact output format.

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
