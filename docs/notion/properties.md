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
| Deal | Buy Box | relation → Buy Box (two-way, synced as Buy Box › Properties) | One version; see `assumptions-and-buy-box.md` |
| Deal | Underwriting | relation → Underwriting (two-way, synced as Underwriting › Property) | Scenarios; see `underwriting.md` |
| Deal | Selected Scenarios | roll-up (count checked) of Underwriting › Selected | Should be 0 or 1 |
| Deal | Multiple Selected | formula: `Selected Scenarios > 1` (checkbox) | Hidden on the record page; drives the view below |
| Deal | Buy Box Fit | formula (text) | Pinned under the title; see "Buy Box Fit" below |
| Buy Box Fit inputs (hidden) | Buy Box Count, Max Purchase Price, Max Scope of Work, Allowed Niche Grades, Min Beds, Min Baths, Min Sq Ft, Min Net Profit, Min Profit % of ARV | roll-ups of the linked Buy Box (count, show original, or sum) | |
| Buy Box Fit inputs (hidden) | Fit Purchase Price, Fit Rehab Estimate, Fit Net Profit, Fit Profit % of ARV | roll-ups (sum) of the Underwriting "Fit" formulas | The Selected scenario's values, 0 without one |
| Media | Photos, Documents | files | |
| Rental (hidden) | Market Rent, Actual Rent | number ($) | |
| Rental (hidden) | Lease End | date | |
| Rental (hidden) | Tenant Name | text | |

Rental fields are hidden in every view and on the record page (page layout visibility "hide").

## Buy Box Fit

Built for issue #8. The formula shows one of:

| Result | When |
|---|---|
| `Fits` | Exactly one Selected scenario and every criterion passes |
| `Fits (physical) – not underwritten` | No Selected scenario and every physical criterion passes |
| `Fails: <criteria>` | One or more criteria fail, listed in this order: Price, SOW, Niche, Beds, Baths, Sq Ft, Profit, Profit % |
| `Check: more than one Selected` | More than one scenario is Selected; the roll-ups would add them together, so nothing is judged. The property is also in "More than one Selected" |
| `No Buy Box` | The property is not linked to exactly one Buy Box version |

With a Selected scenario, Price uses the scenario's Purchase Price, SOW its Rehab Estimate, and Profit and Profit % its Fully loaded Net Profit and Profit % of ARV. Without one, Price uses Asking Price and SOW, Profit and Profit % are skipped. An empty Asking Price, Beds, Baths or Sq Ft, or an empty Niche Grade, counts as a failure, so missing data never reads as a fit.

How it reads the values: formulas cannot read through relations over the API (see `underwriting.md`), so everything arrives as roll-ups. The Buy Box criteria are roll-ups of the linked version. For the Selected scenario, each Underwriting scenario has four hidden "Fit" formulas (for example Fit Net Profit = Net Profit if Selected, else 0), and Properties sums them; with one Selected scenario the sum is that scenario's value.

The Niche check matches the grade inside the formatted Allowed Niche Grades list with commas around it (`,C+,B-,B,` contains `,B-,`), so C does not match C+. The API rejects list functions such as `includes()` on the multi-select roll-up.

## Views

| View | Type | Purpose |
|---|---|---|
| All Properties | table | Every property, rental fields hidden, with Buy Box Fit |
| Pipeline | board | Grouped by Stage; cards show Buy Box Fit |
| ⚠️ Missing Pass Reason | table | Stage is Passed or Lost and Pass Reason is empty. Should be empty. |
| ⚠️ More than one Selected | table | Multiple Selected is checked. Should be empty. |
| 🧪 TEST records | table | Address starts with "TEST –", for manual cleanup; shows Buy Box Fit |
| 🧪 Formula check (Claude) | table | Scratch view for Claude: set its filter to a formula value, then query it in view mode (see below) |

The Acquisitions page also has a linked Pipeline board.

## Board setting

Both Pipeline boards (the database tab and the Acquisitions linked view) have **Hide empty groups** turned off, so all 10 stages show even when empty. The Notion MCP view DSL cannot set this, so it was done by hand in the Notion UI; boards created later through the MCP need the same step.

## Formula and roll-up filters (manual)

The Notion MCP view DSL dropped the checkbox formula filter on "More than one Selected" without an error, so that filter (**Multiple Selected is checked**) was set by hand in the Notion UI (done). A text formula filter set through the DSL (`FILTER "Buy Box Fit" = "Fits"`) is kept, as the "Formula check" view shows. Check the view's returned filter after any MCP change; if a formula or roll-up filter is missing, set it in the UI. A filter edited on a shared view must be saved with "Save for everyone", or it stays private to the editor.

## Reading Buy Box Fit through the MCP

Fetch and query return the formula as a `formulaResult://` reference, and rows/SQL queries count toward the workspace's Query Data Source limit. To check a value without that limit, set the "🧪 Formula check (Claude)" filter with `update-view` (`CLEAR FILTER; FILTER "Buy Box Fit" = "<expected>"`) and query the view in view mode. If the expected property comes back, the value matches.

## Test records

Built for issue #8, all linked to Buy Box v1 and verified through the Formula check view:

| Property | Inputs | Buy Box Fit |
|---|---|---|
| TEST – Fixture D buy box physical fail | Lead, asking $260,000, 2 bd / 1 ba / 1,000 sq ft, Niche C, no scenario | Fails: Price, Niche, Beds |
| TEST – Fixture D2 buy box physical fit | Lead, asking $180,000, 3 bd / 1 ba / 1,000 sq ft, Niche B-, no scenario | Fits (physical) – not underwritten |
| TEST – Buy Box Fit fixture A | Analyzing, 3 bd / 1 ba / 1,100 sq ft, Niche B; one scenario "TEST – Fixture A for Buy Box Fit (Selected, frozen)" with Fixture A inputs, Overrides frozen to the Defaults and Selected | Fails: Profit % (Fixture A's Net Profit of $36,400 is 11.4% of ARV, below 15%) |
| TEST – Select fixtures A F3 (issue #7) | Two Selected scenarios | Check: more than one Selected |

Since issue #11 these properties are linked to Buy Box v2 (Max Purchase Price $275,000), so the Price failures recorded above no longer apply: Fixture D (asking $260,000) now reads "Fails: Niche, Beds", and the others are unchanged. Fixture E (issue #11) is described in `ai-operating-manual.md`.

Fixture A has its own property because the issue #7 property also holds the F3 second Selected scenario. A team member deletes these by hand when they are no longer needed.

Claude creates new properties through the New Lead Intake procedure; see `ai-operating-manual.md`.

## Not built yet

These depend on databases from later issues and are added with them:

- Relations to Projects, Tasks and Knowledge
