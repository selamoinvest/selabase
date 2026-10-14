# Budget Lines database

Built for issue #14. Lives in the Databases page of the "REI Operating System" teamspace.

- Database: https://app.notion.com/p/c82d86586cf04d3d850845e1de30ed07
- Data source: `collection://a3d8510d-d680-42e3-8a1a-363f932a661c`

## Schema

| Field | Type | Allowed values |
|---|---|---|
| Line | title | What the cost is, for example "Roof" or "Rehab – lump" |
| Project | relation → Projects (two-way, synced as Projects › Budget Lines) | |
| Cost Group | select | Acquisition, Rehab, Holding, Financing, Selling |
| Category | select | Demo, Roof, HVAC, Electrical, Plumbing, Kitchen, Bath, Flooring, Paint, Exterior, Permits, Contingency. Rehab lines only |
| Estimate | number ($) | Input |
| Budgeted | formula (number) | Read-only. Accepted bid amount if one exists, else `Estimate` (see below) |
| Budget Source | formula (text) | Read-only. "Accepted bid", "Estimate" or "More than one Accepted bid" |
| Bids | relation → Bids (two-way, synced as Bids › Budget Line) | |
| Accepted Bid Amount, Accepted Bids | roll-up over Bids (max of Accepted Amount; sum of Accepted Count) | Read-only helpers for Budgeted. Hidden from the views |
| Actual | number ($) | Amount paid |
| Variance | formula (number) | Read-only. `Budgeted − Actual`; negative means an overrun |
| Vendor | relation → Companies (two-way, synced as Companies › Budget Lines) | Who was paid |
| Draw # | number | |
| Draw Requested, Draw Inspected, Draw Funded | date | |
| Lien Waiver Received | checkbox | |
| Receipt | files | Proof of cost |
| Budgeted (Group), Actual (Group) for each of the 5 Cost Groups | formula (number) | Read-only helpers for the Project roll-ups (see below). Hidden from the views |

Write inputs only: nobody types into Budgeted or Variance.

**Paid** means Actual is more than 0. There is no separate Paid checkbox.

**Vendor** links to Companies only, not to Contacts, because W-9 on File lives on the Company. A contractor who works alone gets a Company record under their own name.

## Budgeted

Budgeted is the accepted bid amount if one exists, else the Estimate (#15): `if(Accepted Bids > 0, Accepted Bid Amount, Estimate)`. How the bid roll-ups work and the one-Accepted-bid rule are in `bids.md`.

## Project roll-ups

Projects get, for each Cost Group, **[Group] Budgeted**, **[Group] Actual** and **[Group] Variance** (Budgeted − Actual), plus **Total Budgeted**, **Total Actual** and **Total Variance**. View **💰 Budget vs Actual** on Projects shows them side by side.

Why helper fields: the Notion API refuses formulas that read through a relation (`prop("Budget Lines").filter(...).map(...)` gives "Type error with formula", the same as in Underwriting), and a native roll-up cannot filter by Cost Group. So each Budget Line has a helper per group, for example `Actual (Rehab)` = `Actual` if Cost Group is Rehab, else 0. The Project's `Rehab Actual` is a sum roll-up of that helper. The Variance fields are formulas over the Project's own roll-ups.

## Views

| Database | View | Purpose |
|---|---|---|
| Budget Lines | All Budget Lines | Every line, grouped by Cost Group |
| Budget Lines | ⚠️ Paid – no lien waiver | `Actual > 0` and Lien Waiver Received unchecked, sorted by Draw Funded |
| Companies | ⚠️ Paid – no W-9 | Formula `Paid, No W-9 = Yes`: Total Paid > 0 and W-9 on File unchecked |
| Projects | 💰 Budget vs Actual | Budgeted, Actual and Variance per Cost Group and in total |
| Budget Lines | ⚠️ More than one Accepted bid | Data-quality view for #15, see `bids.md` |

Companies has a **Total Paid** roll-up (sum of Actual over its Budget Lines) and the **Paid, No W-9** formula ("Yes" or "No"). The view filters on the formula because the view DSL treats roll-ups as text and does not allow `>` on them.

## Test records

Fixture for issue #14 (Fixture G and Fixture H part 2), all linked to "TEST – 4521 Fixture – Flip Rehab – 2026-10 (10 days)":

| Line | Cost Group | Estimate | Actual | Vendor | Lien Waiver |
|---|---|---|---|---|---|
| TEST – Roof (paid, no waiver) | Rehab / Roof | $12,000 | $11,500 | TEST – Paid vendor, no W-9 | No |
| TEST – Plumbing (paid, waiver in) | Rehab / Plumbing | $8,000 | $8,400 | TEST – Paid vendor, W-9 on file | Yes |
| TEST – Paint (not paid) | Rehab / Paint | $5,000 | | | No |
| TEST – Purchase | Acquisition | $200,000 | | | |

Expected on the Project: Rehab Budgeted $25,000, Rehab Actual $19,900, Rehab Variance $5,100; Acquisition Variance $200,000; Total Variance $205,100.

Checked 2026-10-14:

| Check | Result |
|---|---|
| ⚠️ Paid – no lien waiver lists only "TEST – Roof (paid, no waiver)" (empty before the fixture) | Pass |
| ⚠️ Paid – no W-9 lists only "TEST – Paid vendor, no W-9" (empty before the fixture; the W-9 vendor and the unpaid COI test companies do not show) | Pass |
| Rehab Variance = 5,100 matches the Project; Rehab Variance = 5,101 matches nothing | Pass |
| Line Budgeted = Estimate (before #15; these lines have no bids, so it still holds), line Variance, every Project roll-up and Variance, Companies Total Paid and Paid, No W-9 | Pass (checked by the user in the Notion UI) |

The MCP returns `formulaResult://` and `rollupResult://` references instead of values, so the numbers were checked with filtered row queries until the workspace hit the Notion "Query Data Source" usage limit, then in the Notion UI. Delete the test records by hand when no longer needed.

## Manual steps (Notion UI)

1. **Number formats on formulas and roll-ups.** The API cannot set them, so they show as plain numbers until set by hand (column header → Edit property → Number format → Dollar): Budgeted and Variance on Budget Lines; the six Variance formulas and the Budgeted and Actual roll-ups on Projects; Total Paid on Companies if it is not already in dollars. The hidden helper fields can stay plain. Done by the user 2026-10-14.

## Not built yet

- The Under Contract procedure (#17) creates the starting Budget Lines from the Selected scenario.
- Budget Lines linked views on the Projects pillar page and on each Project page.
- The AI Operating Manual (database map, field dictionary) does not yet list Budget Lines; update it with the Under Contract procedure (#17).
