# Home dashboard

Built for issue #20. The top page of the "REI Operating System" teamspace.

- Page: https://app.notion.com/p/41f226043a8b83978cff815eb9a76b1f

Home links the pillar and System pages, then shows the **Dashboard**: five linked views of databases in the Databases page. Each view shows live data; nothing is copied onto Home.

## Dashboard blocks

| # | View | Database | Filter and sort |
|---|---|---|---|
| 1 | 🔨 Active projects | Projects | Phase is not Closed Out and not Terminated; sorted by Next Deadline |
| 2 | ⏰ Deadlines – next 14 days | Projects | `Due in 14 Days = Yes`, sorted by Next Deadline (same rule as the database view, see `projects.md`) |
| 3 | ✅ My Tasks | Tasks | `Owner = me`, Status not Done or Cancelled; sorted by Due Date |
| 4 | 🏘️ Pipeline by Stage | Properties | Board grouped by Stage; cards show Buy Box Fit, Asking Price, County, Niche Grade |
| 5 | ⚠️ COI expiring (30 days) | Companies | COI Expiration is set and on or before one month from today; sorted soonest first |

"Active" means every Phase except Closed Out and Terminated. Sold / Refinanced / Stabilized stays active, because close-out work (final costs, lien waivers) is still open.

"Me" is the person looking at Home, so one My Tasks block serves every team member. The COI block uses "one month from now", the closest relative date the Notion view filter offers to 30 days (see `crm.md`).

## Manual step

- **Pipeline by Stage: "Hide empty groups" is off**, so all 10 stages show even when empty. The MCP view DSL cannot set it and created the board with empty groups hidden, so a team member turned it off in the Notion UI (board ••• → Group → Hide empty groups). Done on 2026-10-16; the view now returns `hideEmptyGroups: false`. Same step as the other Pipeline boards (`properties.md`).

## Test

Checked 2026-10-16 by querying each Home view in view mode through the Claude account:

| Block | Expected | Result |
|---|---|---|
| Active projects | The four open TEST projects; "TEST – 4521 Fixture – Flip Rehab – 2026-10 (closed out)" (Phase Closed Out, added for this test) does not show | Pass |
| Deadlines – next 14 days | Same three projects as the Projects database view: Fixture F, "(10 days)", "(loan maturity in 5 days)"; "(30 days)" does not show | Pass |
| My Tasks | Same as the Tasks database view: only "TEST – Order inspection" | Pass |
| Pipeline by Stage | All 12 properties, grouped by Stage | Pass |
| COI expiring (Fixture H) | "TEST – COI in 20 days" shows; the 90-day TEST company does not | Pass |

The Fixture H company was created on 2026-10-10 with COI 2026-10-30. For this test its COI Expiration was moved to 2026-11-05, 20 days from the test date.

## Test records

- "TEST – 4521 Fixture – Flip Rehab – 2026-10 (closed out)" in Projects, linked to "TEST – 4521 Fixture Ave". Delete by hand when no longer needed.
