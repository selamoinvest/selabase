# Bids database

Built for issue #15. Lives in the Databases page of the "REI Operating System" teamspace.

- Database: https://app.notion.com/p/94c583f06d0442dc8c9acd6918bbd46a
- Data source: `collection://74bc26ca-f07e-4475-baf7-e2f8641df580`

## Schema

| Field | Type | Allowed values |
|---|---|---|
| Bid | title | Trade – Company, for example "Roof – Acme Roofing" |
| Project | relation → Projects (two-way, synced as Projects › Bids) | |
| Company | relation → Companies (two-way, synced as Companies › Bids) | Who quoted |
| Budget Line | relation → Budget Lines (two-way, synced as Budget Lines › Bids) | The one line this bid prices |
| Trade | select | General, Demo, Framing, Roofing, Plumbing, Electrical, HVAC, Drywall, Flooring, Painting, Carpentry, Concrete, Landscaping (the same list as Companies › Trades) |
| Amount | number ($) | Quoted price |
| Scope Summary | text | What the quote covers, so bids for one trade can be compared |
| Quote PDF | files | |
| Status | select | Requested, Received, Accepted, Rejected |
| Accepted Amount | formula (number) | Read-only helper: Amount if Status is Accepted, else 0. Hidden from the views |
| Accepted Count | formula (number) | Read-only helper: 1 if Status is Accepted, else 0. Hidden from the views |

Link each bid to exactly one Budget Line. A bid that covers several lines (for example one contractor for Kitchen and Bath) gets one bid record per line, with the amount split.

## How Budgeted reads the accepted bid

Budget Lines has two roll-ups over Bids: **Accepted Bid Amount** (max of Accepted Amount) and **Accepted Bids** (sum of Accepted Count). Then:

- **Budgeted** = `if(Accepted Bids > 0, Accepted Bid Amount, Estimate)`.
- **Budget Source** = "Accepted bid", "Estimate", or "More than one Accepted bid".

Marking a bid Accepted changes Budgeted at once. Marking it Rejected (or anything but Accepted) sends Budgeted back to Estimate. Nobody types into Budgeted.

The helpers exist because the Notion API refuses formulas that read through a relation (see `budget-lines.md`), so the Accepted filter sits on the Bid and the roll-up only sums.

**One Accepted bid per line.** Notion cannot enforce it. With two Accepted bids, Budgeted takes the larger one (the roll-up is max, not sum, so amounts are never added up), Budget Source says "More than one Accepted bid" and the line shows in the **⚠️ More than one Accepted bid** view on Budget Lines. That view should be empty. Fix it by setting the extra bid to Rejected.

## Views

| Database | View | Purpose |
|---|---|---|
| Bids | All Bids | Every bid, grouped by Trade, cheapest first, with Scope Summary for comparing |
| Budget Lines | ⚠️ More than one Accepted bid | Data-quality view: `Budget Source = More than one Accepted bid` |

The All Budget Lines view now also shows Budget Source and Bids.

## Test records

Fixture F2. The Fixture F Project does not exist until the Under Contract procedure (#17), so the line hangs on "TEST – 4521 Fixture – Flip Rehab – 2026-10 (30 days)" (the "10 days" project holds the #14 roll-up fixture, whose totals stay unchanged):

| Record | Database | Values |
|---|---|---|
| TEST – Roof (F2 bids) | Budget Lines | Rehab / Roof, Estimate $12,000 |
| TEST – Roof – Roofer $11,000 | Bids | Roofing, $11,000, Company "TEST – Roofer $11,000", now Rejected |
| TEST – Roof – Roofer $13,500 | Bids | Roofing, $13,500, Company "TEST – Roofer $13,500", Received |

Checked 2026-10-14:

| Check | Result |
|---|---|
| $11,000 bid Accepted: Budgeted = 11,000 matches the line; Budgeted = 12,000 matches nothing; Budget Source = "Accepted bid" | Pass |
| $11,000 bid Rejected again: Budgeted = 12,000 matches the line | Pass |
| Both bids Accepted: Budgeted = 13,500 (the larger, not the 24,500 sum) and the line shows in ⚠️ More than one Accepted bid; after resetting to one Rejected and one Received the view is empty | Pass |

How it was checked: the MCP returns `formulaResult://` references instead of values, the "Query Data Source" usage limit was reached, and the view DSL drops filters on number formulas. So a temporary text formula `format(Budgeted)` was added to Budget Lines, a view filtered on it was queried in view mode (which has no usage limit), and the temporary column was dropped afterwards. That check view became the ⚠️ More than one Accepted bid view. The failing run before the formula change was not observed: the first check query hit the usage limit, and the text-formula method was found only after the formula had changed. The Rejected check (Budgeted back to 12,000 after 11,000) shows the formula reacts to Status.

Delete the test records by hand when no longer needed.

## Manual steps (Notion UI)

1. **Number format.** Set Accepted Bid Amount on Budget Lines to Dollar if you show it (column header → Edit property → Number format → Dollar). Budgeted is already on the list in `budget-lines.md`.

## Not built yet

- The Compare Bids procedure (user story 52): Claude compares bids for one trade and points out scope differences. It belongs in the AI Operating Manual.
- A Bids linked view on the Projects pillar page and on each Project page.
- The AI Operating Manual (database map, field dictionary) does not yet list Bids.
