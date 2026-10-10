# Underwriting database

Built for issue #6. Lives in the Databases page of the "REI Operating System" teamspace.

- Database: https://app.notion.com/p/a6c3e4dd3bc1474b8ee3cbd791288a12
- Data source: `collection://ae5d408e-122a-4a66-976a-fe615773dd99`

Write inputs and overrides only. Every Default is a roll-up and every Effective value and result is a formula.

## Schema

### Inputs

| Field | Type | Notes |
|---|---|---|
| Scenario | title | For example "Flip – conservative ARV" |
| Property | relation → Properties (two-way, synced as Properties › Underwriting) | One property |
| Assumptions | relation → Assumptions (two-way, synced as Assumptions › Underwriting) | The Active record |
| Buy Box | relation → Buy Box (two-way, synced as Buy Box › Underwriting) | The Active version |
| Exit Strategy | select | Flip, Rental (Rental formulas are not built) |
| Selected | checkbox | Freeze and one-per-property come with issue #7 |
| Purchase Price, ARV, Rehab Estimate, Monthly Holding Cost | number ($) | Monthly Holding Cost has no default |

### Assumptions (Default / Override / Effective)

Each of the eight assumptions has three fields:

| Field | Type | Meaning |
|---|---|---|
| `<Assumption> Default` | roll-up (sum) of the linked Assumptions record | The central value |
| `<Assumption> Override` | number | Optional, per scenario |
| `<Assumption> Effective` | formula | Override if filled in, else Default |

The assumptions are Buy Closing %, Selling %, Hard Money Rate, Points %, Hold Months, Rehab Contingency %, LTC Purchase % and LTC Rehab %.

The Effective formula tests `length(format(Override)) > 0`, not `empty(Override)`. Notion treats `empty(0)` as true, so a 0% override (for example a frozen 0% contingency) would otherwise fall back to the Default.

### Results

| Field | Formula |
|---|---|
| Buy Closing | Purchase × Buy Closing % |
| Base Profit | ARV − Purchase − Buy Closing − Rehab |
| Loan Amount | LTC Purchase % × Purchase + LTC Rehab % × Rehab |
| Points | Loan Amount × Points % |
| Interest | Loan Amount × Rate × Hold Months ÷ 12 |
| Holding | Monthly Holding Cost × Hold Months |
| Contingency | Rehab × Rehab Contingency % |
| Total Project Cost | Purchase + Buy Closing + Rehab + Contingency + Holding + Points + Interest |
| Selling Costs | ARV × Selling % |
| Net Profit | ARV − Selling Costs − Total Project Cost (Fully loaded) |
| Profit % of ARV | Net Profit ÷ ARV (0 when ARV is empty) |
| Cash Invested | Total Project Cost − Loan Amount |
| ROI | Net Profit ÷ Cash Invested (0 when Cash Invested ≤ 0) |
| MAO | ARV × 70% − Rehab |
| Below Criteria | Checkbox: a Buy Box is linked, ARV > 0, and Net Profit < Min Net Profit or Profit % of ARV < Min Profit % of ARV |

Dollar results are rounded to cents. Min Net Profit and Min Profit % of ARV are roll-ups from the linked Buy Box.

The record page groups fields into Inputs, Results – Base, Results – Fully loaded, Assumptions and Buy Box criteria. Property, Selected and Below Criteria are pinned under the title.

## Why roll-ups, not formulas through the relation

The spec says formulas read values through relations. The Notion API refuses `prop("Assumptions").first().prop("Selling %")` with "Type error with formula", so the Defaults are roll-ups (sum over the one linked record) and the formulas read the roll-ups. The result is the same as long as a scenario links exactly one Assumptions record and one Buy Box version.

## Views

| Where | View | Purpose |
|---|---|---|
| Underwriting | All Scenarios | Inputs and main results |
| Underwriting | 🧪 TEST records | Scenario starts with "TEST –", for manual cleanup |
| Acquisitions page | Underwriting | Linked view with the main results |

## Manual steps (Notion UI)

The Notion MCP cannot do these:

1. **Database template.** In Underwriting, create a template named "New scenario" with Assumptions = "Defaults" and Buy Box = "v1 – Oct 2026" and Exit Strategy = Flip, and make it the default template. When the Active Assumptions record or Buy Box version changes (Update Buy Box, issue #11), point the template at the new record.
2. **Number formats on formulas.** Set the result formulas to Dollar, and the Effective %, Profit % of ARV and ROI formulas to Percent. The API cannot set formula formats, so they show as plain numbers until this is done.
3. **Relation limits (optional).** Limit Property, Assumptions and Buy Box to 1 page each.

## Reading formula values through the MCP

Fetch and query return formula and roll-up values as `formulaResult://` / `rollupResult://` references, not numbers, and SQL mode cannot see them. To check a value, use `query-data-sources` in rows mode with a formula filter, for example Net Profit `number_equals` 36400. If the expected row comes back, the value matches.

## Test records

On the property "TEST – Underwriting fixtures A2 B C, St. Louis, MO 63110" (Stage Analyzing, Niche B, 3 bd / 1 ba / 1,100 sq ft, Buy Box v1):

| Scenario | Inputs | Verified |
|---|---|---|
| TEST – Fixture A2 (defaults) | 200k / 320k ARV / 35k rehab / 500 per month | Buy Closing 6,000; Base Profit 79,000; Loan 175,000; Points 3,500; Interest 10,500; Holding 3,000; Contingency 0; Total Project Cost 258,000; Selling Costs 25,600; Net Profit 36,400; Cash Invested 83,000; ROI between 43.85% and 43.86%; MAO 189,000; Below Criteria Yes |
| TEST – Fixture B (Selling override 6%) | Same, Selling % Override 6% | Selling Costs 19,200; Net Profit 42,800; Below Criteria Yes |
| TEST – Zero override (Points 0%) | Same, Points % Override 0% | Points 0; Net Profit 39,900 (the 0% override is used, not the 2% default) |

Fixture C: with the central Selling % set to 7%, A2 showed Net Profit 39,600 and B stayed at 42,800. Selling % was then restored to 8% and read back as 0.08. The A2 check after the restore (back to 36,400) did not run because the workspace hit its query usage limit. The value is the same as the first A2 check.

Fixture A (Selected, frozen) belongs to issue #7.

A team member deletes these by hand when they are no longer needed.
