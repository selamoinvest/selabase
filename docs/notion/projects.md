# Projects database

Built for issue #12. Lives in the Databases page of the "REI Operating System" teamspace.

- Database: https://app.notion.com/p/7b30749ae23447708084aad945e7c10f
- Data source: `collection://2e95539e-c1c3-4af4-925c-d6d87ffdbe0c`

## Schema

| Group | Field | Type | Allowed values |
|---|---|---|---|
| Core | Title | title | "[short address] – [Type] – [YYYY-MM]", for example "1234 Main – Flip Rehab – 2026-11" |
| Core | Type | select | Flip Rehab, BRRRR Rehab, Rental Turnover, CapEx Repair |
| Core | Phase | select | Due Diligence, Closing, Pre-Construction, Rehab, Punch List, Listed / Lease-Up, Sold / Refinanced / Stabilized, Closed Out, Terminated |
| Core | Property | relation → Properties (two-way, synced as Properties › Projects) | |
| Milestones | EMD Due, Inspection Deadline, Appraisal Date, Financing Contingency Deadline, Closing Date, Rehab Start, Target Rehab Complete, Actual Rehab Complete, Occupancy Inspection, List Date, Sale Closed, Loan Maturity Date | date | |
| Occupancy | Occupancy Inspection Required? | checkbox | |
| Occupancy | Occupancy Inspection Status | select | Scheduled, Passed, Failed, Re-inspect |
| Occupancy | Escrow Amount | number ($) | |
| Hard money loan | Lender | relation → Companies (two-way, synced as Companies › Projects (as lender)) | |
| Hard money loan | Loan Amount, Extension Fee, Rehab Holdback | number ($) | |
| Hard money loan | Rate, Points | number (%) | |
| Hard money loan | Term | number | Months |
| Deadlines | Next Deadline | formula (date) | Read-only |
| Deadlines | Due in 14 Days | formula (text) | Read-only; "Yes" or "No" |
| Budget | Budget Lines | relation → Budget Lines (two-way, synced as Budget Lines › Project) | |
| Budget | Acquisition, Rehab, Holding, Financing, Selling, Total × Budgeted, Actual | roll-up (sum) | Read-only |
| Budget | Acquisition, Rehab, Holding, Financing, Selling, Total × Variance | formula (number) | Read-only; Budgeted − Actual, negative means an overrun |

How the budget roll-ups work is in `budget-lines.md`.

The spec lists "Maturity Date" under the loan fields and "Loan Maturity Date" under the milestones. They are the same date, so there is one field, **Loan Maturity Date**.

Loan and draw fields are provisional (spec): review them on the first real project.

## Deadlines in the next 14 days

- **Next Deadline**: the earliest of the 12 milestone dates that is today or later (empty when none is).
- **Due in 14 Days**: "Yes" when Next Deadline is 14 or fewer days from today, else "No".
- View **⏰ Deadlines – next 14 days** filters `Due in 14 Days = Yes`, sorted by Next Deadline. Actual dates (Actual Rehab Complete, Sale Closed) are in the past once entered, so they drop out on their own.

Why a formula: the Notion view filter only offers "one week from now" and "one month from now" as relative dates, so a native "next 14 days" filter across 12 fields is not possible.

A project shows if any of its dates is in the window, even when an earlier date is already past, because only dates from today on count.

## Views

| View | Type | Purpose |
|---|---|---|
| All Projects | table | Every project with type, phase, property, next deadline, closing and loan |
| ⏰ Deadlines – next 14 days | table | Projects with any milestone or loan maturity in the next 14 days |
| 💰 Budget vs Actual | table | Budgeted, Actual and Variance per Cost Group and in total (#14) |

## Test records

Fixture for issue #12, all linked to "TEST – 4521 Fixture Ave" (checked 2026-10-14 by querying the 14-day view):

| Project | Dates | In 14-day view |
|---|---|---|
| TEST – 4521 Fixture – Flip Rehab – 2026-10 (10 days) | Inspection 2026-10-24, Closing 2026-12-15 | Yes |
| TEST – 4521 Fixture – Flip Rehab – 2026-10 (30 days) | Inspection 2026-11-13 | No |
| TEST – 4521 Fixture – Flip Rehab – 2026-10 (loan maturity in 5 days) | Loan Maturity 2026-10-19, EMD 2026-09-01 (past) | Yes |

The Property's Projects field lists all three. The test records are dated relative to 2026-10-14; the 14-day view will stop showing them as time passes. Delete by hand when no longer needed.

## Not built yet

- Relations from Bids and Knowledge, added with those databases (Tasks added in #13, Budget Lines and the budget roll-ups in #14).
- A Projects linked view on the Projects pillar page, and the Home "Deadlines" and "Active projects" views.
- The Notion MCP cannot confirm formula values directly (it returns `formulaResult://` references), so the fixtures were checked through the view's membership.
- The AI Operating Manual (database map, field dictionary, naming rules) does not yet list Projects; update it with the Under Contract procedure (#17).
