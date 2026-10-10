# Properties database

Built for issue #3. Lives in the Databases page of the "REI Operating System" teamspace.

- Database: https://app.notion.com/p/7c5ff736e80e468687993208ad011329
- Data source: `collection://2f3b95a3-127c-4934-82df-f7b5bed76f4b`

## Schema

| Group | Field | Type | Allowed values |
|---|---|---|---|
| Location | Address | title | Full address |
| Location | County | select | St. Louis City, St. Louis County, St. Charles, Jefferson, Franklin |
| Location | Municipality, Parcel ID / Locator #, Zoning, Flood Zone | text | |
| Physical | Beds, Baths, Sq Ft, Year Built | number | |
| Physical | Property Type | select | SFR, 2–4 Unit, Condo |
| Deal | Stage | select | Lead, Analyzing, Walkthrough Scheduled, Offer Submitted, Negotiating, Under Contract, Owned, Sold, Passed, Lost |
| Deal | Pass Reason | select | Price, Rehab Too Heavy, Location, Title, Lost to Other Buyer |
| Deal | Asking Price | number ($) | |
| Deal | Condition | select | Turnkey, Light, Medium, Heavy, Gut |
| Deal | Lead Source | select | MLS, Wholesaler, Direct Mail, Driving for Dollars, LRA, Tax Sale, Referral |
| Deal | Niche Grade | select | A+, A, A-, B+, B, B-, C+, C, C-, D+, D, D-, F |
| Media | Photos, Documents | files | |
| Rental (hidden) | Market Rent, Actual Rent | number ($) | |
| Rental (hidden) | Lease End | date | |
| Rental (hidden) | Tenant Name | text | |

Rental fields are hidden in every view and on the record page (page layout visibility "hide").

## Views

| View | Type | Purpose |
|---|---|---|
| All Properties | table | Every property, rental fields hidden |
| Pipeline | board | Grouped by Stage |
| ⚠️ Missing Pass Reason | table | Stage is Passed or Lost and Pass Reason is empty. Should be empty. |
| 🧪 TEST records | table | Address starts with "TEST –", for manual cleanup |

The Acquisitions page also has a linked Pipeline board.

## Board setting

Both Pipeline boards (the database tab and the Acquisitions linked view) have **Hide empty groups** turned off, so all 10 stages show even when empty. The Notion MCP view DSL cannot set this, so it was done by hand in the Notion UI; boards created later through the MCP need the same step.

## Not built yet

These depend on databases from later issues and are added with them:

- Buy Box Fit (formula) and the Buy Box relation
- Relations to Underwriting, Projects, Tasks and Knowledge
- The roll-up that counts Selected scenarios
