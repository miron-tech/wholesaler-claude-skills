# County Config — Harris County, TX

Last verified: 2026-03-08

## Assessor (HCAD)

- Portal: https://search.hcad.org/
- Parent site: https://hcad.org/ → PROPERTY SEARCH nav link → redirects to search.hcad.org
- Search type: Address (default tab), Owner Name, Account Number, Advanced
- Search format: "4726 Cashel Castle Dr" (street number + street name, no city/zip needed)
- Results: Click account number to open detail page
- Load time: 2-3 seconds for search, 3-5 seconds for detail page

### Data Available
- Account number
- Owner name + mailing address
- Legal description
- Lot size (SF)
- Living area (SF)
- Year built
- Property type + quality grade
- Foundation type
- Exterior wall type
- Heating/AC type
- Room count (bedrooms, bathrooms, total)
- Fireplace count + type
- Garage size
- Building areas breakdown (base, porch, garage, upper)
- Ownership history (full chain with dates)
- Tax jurisdictions + rates (all districts)
- Exemption status
- Valuation history (by year)
- Land details (use codes, unit prices)

### URL Patterns
- Main: https://hcad.org/
- Search: https://search.hcad.org/
- Detail: https://search.hcad.org/SearchResults/{encoded-token} (dynamic, not bookmarkable)
- OLD URLs (BROKEN — do not use):
  - public.hcad.org/records/quicksearch.asp → 404
  - public.hcad.org/records/Real.asp → 404
  - public.hcad.org/records/details.asp → 500
  - arcgis.hcad.org/propertysearch → Error
  - hcad.org/property-search → 404

### Quirks
- Detail page URLs are session-encoded tokens, not permanent links
- 2026 values show "Pending" until certification (typically May-June)
- Texas law prohibits displaying residential photos, sketches, or owner age on their website
- Fiduciary field exists but usually shows "None"
- Bedroom/bath count sometimes differs from MLS — HCAD is more reliable for structural data

## Tax Office

- Harris County Tax Office: https://www.hctax.net/
- Not tested in this session
- Tax delinquency data should be available here
- Combined tax rate for this area: ~2.294% (10 jurisdictions)

## Key Jurisdictions (77069 / Champions area)
- Klein ISD (017) — rate 1.011900
- Harris County (040) — rate 0.380960
- Harris Co Flood Control (041) — rate 0.049660
- Port of Houston Authority (042) — rate 0.005900
- Harris Co Hospital District (043) — rate 0.187610
- Harris Co Education Dept (044) — rate 0.004798
- Lone Star College (045) — rate 0.106000
- HC MUD 48 (348) — rate 0.409477
- HC Emergency Serv Dist 29 (645) — rate 0.100000
- HC Emergency Serv Dist 11 (666) — rate 0.038078

## Code Violations

- Houston does NOT have a Socrata open data endpoint for code violations
- Houston 311: https://www.houstontx.gov/311/
- May need to call or use Chrome to search city portal
