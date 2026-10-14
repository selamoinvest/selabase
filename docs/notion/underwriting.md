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
| Selected | checkbox | The scenario the team acts on; one per property. See "Select a scenario" below |
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

Four hidden formulas feed Buy Box Fit on Properties: Fit Purchase Price, Fit Rehab Estimate, Fit Net Profit and Fit Profit % of ARV each give the value when Selected is ticked, else 0. Properties sums them per property; see "Buy Box Fit" in `properties.md`.

Dollar results are rounded to cents. Min Net Profit and Min Profit % of ARV are roll-ups from the linked Buy Box.

The record page groups fields into Inputs, Results – Base, Results – Fully loaded, Assumptions and Buy Box criteria. Property, Selected and Below Criteria are pinned under the title.

## Select a scenario (freeze)

Built for issue #7. Notion has no trigger the MCP can set up, so freezing is a step in the Select Scenario procedure, not an automation:

1. If another scenario on the same Property is Selected, ask before unticking it (it holds agreed numbers).
2. Copy every Effective value into its Override field. An Override that is already filled in is the Effective value, so leave it as it is. For each empty Override, the Effective value is the Default, which is the value on the linked Assumptions record: read it from that record (fetch returns plain numbers, unlike the formulas) and write it to the Override.
3. Tick Selected.

After this, the scenario's Effective values come from its own Overrides, so a change to the central Assumptions record does not change its results. Unticking Selected does not clear the Overrides; clearing them is a deliberate manual action.

Ticking Selected by hand in Notion does not freeze. Only the procedure does.

Each Property counts its Selected scenarios; see "Selected Scenarios" in `properties.md`.

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

1. **Database template (done).** "New scenario" (https://app.notion.com/p/3f5226043a8b80b2a7edcb6e6e93622e, template ID `3f522604-3a8b-80b2-a7ed-cb6e6e93622e`) prefills Assumptions = "Defaults", Buy Box = the Active version (v2 – Oct 2026 since issue #11) and Exit Strategy = Flip, and is the default template. A default template applies when someone clicks New in Notion; when Claude creates a scenario through the MCP it passes this `template_id` or sets the two relations itself. When the Active Assumptions record or Buy Box version changes (Update Buy Box, issue #11), point the template at the new record.
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

On the property "TEST – Select fixtures A F3, St. Louis, MO 63110" (Stage Analyzing, Niche B, 3 bd / 1 ba / 1,100 sq ft, Buy Box v1), built for issue #7:

| Scenario | Inputs | State |
|---|---|---|
| TEST – Fixture A (Selected, frozen) | 200k / 320k ARV / 35k rehab / 500 per month | Select Scenario run on it. All eight Overrides read back as 3%, 8%, 12%, 2%, 6, 0%, 70%, 100%, and Selected is ticked |
| TEST – Fixture F3 (second Selected, ticked by hand) | 190k / 320k ARV / 35k rehab / 500 per month | Selected ticked by hand, no Overrides. The property now has two Selected scenarios |

The Query Data Source usage limit blocked Claude from reading formulas for issue #7, so a team member checked these by eye in the Notion UI:

- Fixture A after the freeze: Net Profit 36,400 and all eight Overrides filled.
- Fixture C on Fixture A: with the central Selling % at 7%, A stayed at 36,400 and A2 moved to 39,600. Selling % was then restored, and Claude read it back as 0.08.

Fixture F3, part 1, is verified: a view-mode query of "⚠️ More than one Selected" (view mode applies the view's own filter and does not count toward the query limit) returned only the F3 property. "TEST – Underwriting fixtures A2 B C", which has scenarios but none Selected, was not returned.

Part 2 of F3 (Claude asks before unticking) is covered by the Select Scenario procedure in the AI Operating Manual (issue #10).

A team member deletes these by hand when they are no longer needed.

Issue #10: the yes path of F3 was run on this property. Fixture F3 is now Selected and frozen (Net Profit 47,260 by hand) and Fixture A is unselected.
