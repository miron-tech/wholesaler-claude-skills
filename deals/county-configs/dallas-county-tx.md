# Dallas County, TX — County Site Config

Last Verified: 2026-03-08

## Assessor (DCAD)
- URL: https://www.dallascad.org/SearchAddr.aspx
- Search by: Street Number + Street Name (no suffix needed)
- City dropdown: Select "DALLAS" from list
- Quirks: Don't enter street suffix (Dr, Ln, etc). Direction dropdown is optional.
- Detail page sections: Property Location, Legal Desc, Value, Owner, Main Improvement, Land, Exemptions, Estimated Taxes, History
- History page: Click "History" link at bottom → shows Owner history, Market Value history, Taxable Value history by year
- Account URL pattern: https://www.dallascad.org/AcctDetailRes.aspx?ID=[PARCEL_NUMBER]
- History URL pattern: https://www.dallascad.org/AcctHistory.aspx?ID=[PARCEL_NUMBER]
- DCAD loads slow — wait 3-5 seconds after search before reading results

## Tax Collector (Dallas County Tax Office)
- URL: https://www.dallasact.com/act_webdev/dallas/searchbyproperty.jsp
- Search by: Street Number + Street Name (suffix dropdown is optional, can leave as "----------")
- Quirks: Suffix dropdown has trailing spaces in values — may fail with form_input tool. Just skip it and search without suffix.
- Shows: Current Tax Levy, Current Amount Due, Prior Year Amount Due, Total Amount Due
- Detail page has links to: Current Tax Statement, Summary Tax Statement, Taxes Due Detail by Year and Jurisdiction
- Account URL pattern: https://www.dallasact.com/act_webdev/dallas/showdetail2.jsp?can=[ACCOUNT_NUMBER]&ownerno=0

## Register of Deeds / County Clerk
- URL: Not verified
- Dallas County Clerk: https://www.dallascounty.org/departments/countyclerk/
- Online deed search: Need to verify availability

## Code Violations (Dallas OpenData — Socrata API)
- **API — Code Violations (primary):** `https://www.dallasopendata.com/resource/x9pz-kdq9.json?str_num=[HOUSE_NUMBER]&str_nam=[STREET_NAME_UPPERCASE]&$limit=20`
- **API — 311 Requests (supplement):** `https://www.dallasopendata.com/resource/eaah-n7x2.json?$where=address like '%[HOUSE_NUMBER] [STREET_NAME_UPPERCASE]%'&$limit=20`
- Search by: str_num + str_nam (CAPS, no suffix — e.g., str_nam=HAPPY CANYON)
- No auth needed, returns JSON, free
- **Chrome fallback (Accela):** `https://aca-prod.accela.com/DALLASTX/APO/APOLookup.aspx?TabName=Home`
- Phone fallback: Dallas Code Compliance at 214-670-5708 or 311
- Tested 2026-03-08: API returned 1 closed violation for 3846 Happy Canyon Dr (oversized vehicle, 2017)
