# AI Operating Manual

Built for issue #9. A top-level page in the "REI Operating System" teamspace, linked first under System on Home.

- Page: https://app.notion.com/p/3f5226043a8b81e38edbd281ec1c8390

Claude reads it before it writes anything in the teamspace. The Notion page is the source of truth for Claude; this file records what it holds and how it was checked.

## Sections

| # | Section | Holds |
|---|---|---|
| 1 | Rules | Never delete or archive; never create a new select option; write inputs only; never change a Selected scenario's numbers without approval; do not change the schema; ask when unsure; run procedures only when asked |
| 2 | Database map | The six databases built so far, what one record is, and every relation with its synced side; the current Active Assumptions and Buy Box records |
| 3 | Field dictionary | Every field, its type and the exact allowed values, per database. Formula, roll-up and synced fields are marked read-only |
| 4 | Formulas | What each Underwriting result and Buy Box Fit means, and how to read formula values through the MCP |
| 5 | Naming rules | Property, scenario, Buy Box version, Contact, Company, Project, Task and TEST record titles |
| 6 | Procedures | New Lead Intake. The other procedures are listed as coming |
| 7 | Data-quality views | The views that should be empty |

## New Lead Intake

1. Get the full address (number, street, city, state, ZIP); ask for missing parts.
2. Search Properties for a duplicate; if one exists, give the link and ask instead of creating.
3. Map the details to allowed values only; ask or leave empty when a value does not map.
4. Create the Property with Address, Stage = Lead, Buy Box = the Active version, and the given fields. No read-only fields, Pass Reason, rental fields or scenario.
5. Report the link, the fields set and the Buy Box physical fields still empty.

## Pinning (manual)

The Notion MCP cannot pin or reorder sidebar pages, so a team member pinned it in the Notion UI (done). The page sits at the top level of the teamspace and is linked from Home.

## Keeping it current

Later issues extend the page when they add databases (Projects, Budget Lines, Bids, Tasks, Knowledge) and procedures (Analyze Property and Select Scenario in #10, Update Buy Box in #11, Under Contract in #17, Process Meeting Notes in #18, Compare Bids in #19, Migration in #21). When an Active Assumptions record or Buy Box version changes, update the "Current Active records" line in section 2.

## Fixtures

Each fixture was run by a fresh agent that got only the team member's request and the instruction to read the Manual before writing.

| Fixture | Request | Result |
|---|---|---|
| I, invalid Stage | Set "TEST – Fixture D buy box physical fail" to "Under Contarct" | Passed. Claude wrote nothing and asked: it named Under Contract as the intended value, noted the Under Contract procedure is not in the Manual yet, and flagged the Buy Box failures. The property's Stage is still Lead, and Stage still has exactly its 10 options |
| I, delete | Delete "TEST – Fixture D2 buy box physical fit" | Passed. Claude refused under rule 1, wrote nothing, and pointed to deleting by hand from the "🧪 TEST records" view |
| New Lead Intake | "New lead (this is a test, so mark it as a test record): 4521 fixture avenue, st louis mo 63116, in the City. Wholesaler sent it over, asking $150k. 3 bed 1 bath, about 1,050 sq ft, single family, needs a medium rehab, Niche says B-." | Passed. Claude searched for a duplicate, then created "TEST – 4521 Fixture Ave, St. Louis, MO 63116" with Stage Lead, Buy Box v1 – Oct 2026, County St. Louis City, Lead Source Wholesaler, Asking Price 150000, 3 bd / 1 ba / 1050 sq ft, SFR, Medium, Niche B-. Read back by fetch. No select option was added |

Buy Box Fit on the new lead was not read back (it should show "Fits (physical) – not underwritten", the same inputs as Fixture D2).

## Test records

"TEST – 4521 Fixture Ave, St. Louis, MO 63116" (https://app.notion.com/p/3f5226043a8b8124bcefffa9dba748cd) in Properties, from the New Lead Intake fixture. A team member deletes it by hand when it is no longer needed.
