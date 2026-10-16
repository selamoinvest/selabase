# AI Operating Manual

Built for issue #9. A top-level page in the "REI Operating System" teamspace, linked first under System on Home.

- Page: https://app.notion.com/p/3f5226043a8b81e38edbd281ec1c8390

Claude reads it before it writes anything in the teamspace. The Notion page is the source of truth for Claude; this file records what it holds and how it was checked.

## Sections

| # | Section | Holds |
|---|---|---|
| 1 | Rules | Never delete or archive; never create a new select option; write inputs only; never change a Selected scenario's numbers without approval; do not change the schema; ask when unsure; run procedures only when asked |
| 2 | Database map | All eleven databases (Projects, Budget Lines, Bids and Tasks listed with #17, Knowledge in #16), what one record is, and every relation with its synced side; the current Active Assumptions and Buy Box records |
| 3 | Field dictionary | Every field, its type and the exact allowed values, per database, including Knowledge and the glossary pointer (#16) and Projects, Budget Lines, Bids and Tasks (#17). Formula, roll-up and synced fields are marked read-only |
| 4 | Formulas | What each Underwriting result and Buy Box Fit means, and how to read formula values through the MCP |
| 5 | Naming rules | Property, scenario, Buy Box version, Contact, Company, Project (short address and month defined in #17), Task, Budget Line, Bid, Knowledge and TEST record titles |
| 6 | Procedures | New Lead Intake, Analyze Property, Select Scenario, Update Buy Box, Answer from Knowledge, Under Contract, Process Meeting Notes, Compare Bids. The other procedures are listed as coming |
| 7 | Data-quality views | The views that should be empty, including the Budget Lines and W-9 views (#17), plus a pointer to the 14-day deadlines view |

## New Lead Intake

1. Get the full address (number, street, city, state, ZIP); ask for missing parts.
2. Search Properties for a duplicate; if one exists, give the link and ask instead of creating.
3. Map the details to allowed values only; ask or leave empty when a value does not map.
4. Create the Property with Address, Stage = Lead, Buy Box = the Active version, and the given fields. No read-only fields, Pass Reason, rental fields or scenario.
5. Report the link, the fields set and the Buy Box physical fields still empty.

## Analyze Property (issue #10)

1. Find the Property (ask if none or several; never create one here).
2. Set Stage = Analyzing; ask first if the Stage is Under Contract or later.
3. Create the scenario from the "New scenario" template, linked to the Property; check Assumptions, Buy Box and Exit Strategy.
4. Ask for the missing inputs (Purchase Price, ARV, Rehab Estimate, Monthly Holding Cost) in one message.
5. Write those four inputs only.
6. Read results back and report them, stating Below Criteria plainly and which minimum it misses.
7. Report the Buy Box Fit (physical, since not Selected), naming failed physical criteria; offer Select Scenario.

## Select Scenario (issue #10)

1. Identify the scenario and its Property.
2. If another scenario is Selected, ask before unticking it; on "no", change nothing.
3. Freeze: copy each Effective assumption into its empty Override (0 counts as filled in), reading Defaults from the Assumptions record.
4. Tick Selected.
5. Report what was frozen and unticked, the resulting Buy Box Fit (with failed criteria) and Below Criteria.

## Update Buy Box (issue #11)

1. Find the Active version (stop and ask if none or several).
2. Get the changed criteria; copy the rest from the Active version and say so.
3. Name it "v[n] – [Mon YYYY]".
4. Create it with Active unchecked.
5. List the pre-contract properties (Lead to Negotiating) linked to the old version; state that later stages stay; ask for a yes.
6. After the yes, tick Active on the new version and untick it on the old.
7. Set Buy Box to the new version on those properties and on their scenarios; write nothing else.
8. Point the "New scenario" template at the new version and update the "Current Active records" line.
9. Report the version, properties moved (with Buy Box Fit), properties left behind, and the template.

Moving a scenario's Buy Box (step 7) is a choice made here, so Below Criteria reads the same criteria as Buy Box Fit. It changes no input or Override.

## Under Contract (issue #17)

1. Find the Property; stop and ask if its Stage is Under Contract or later, or if it already has a Project that is not Terminated.
2. Find the one Selected scenario; stop if there is none, more than one, or any Override is empty (not frozen).
3. Calculate the eight line amounts from the scenario's inputs and Overrides (rounded to cents), then check them against Notion before writing: a rows query with Total Project Cost and Selling Costs `number_equals` filters must return the scenario. The formulas do not round, so on a miss the query is repeated with a ±0.01 range before Claude stops.
4. Set Stage = Under Contract.
5. Create the Project: "[short address] – [Type] – [YYYY-MM]", Type from Exit Strategy (Flip → Flip Rehab), Phase = Due Diligence, Property linked. Lender and loan fields stay empty.
6. Create the eight Budget Lines with the amount in Estimate: Purchase and Buy Closing (Acquisition); Rehab – lump and Contingency (Rehab, Category Contingency on the second); Holding (Holding); Points and Interest (Financing); Selling Costs (Selling). A 0 line is still written.
7. Create the seven due-diligence Tasks (Not Started, High, Property and Project linked).
8. Ask for the contract dates and write them to the Project; Deposit EMD is due on EMD Due and Order inspection on Inspection Deadline.
9. Report the links, the lines and their total (= ARV − Net Profit), the tasks, the dates and what is still empty.

Why amounts are calculated by hand: the MCP returns formula values as `formulaResult://` references, so Claude cannot copy Buy Closing, Points, Interest etc. from the scenario. A Selected scenario is frozen, so its Overrides hold every assumption as a plain number and the hand calculation uses exactly what the formulas use. The rows query in step 3 makes Notion confirm the result before anything is written.

Why a Rehab lump: the spec splits Rehab into categories later, as bids arrive (Fixture F2 splits a Roof line out of it).

## Process Meeting Notes (issue #18)

1. Find the note by a rows query on Knowledge (Type = Meeting Note, Title contains); ask on no match or several.
2. Read the page body and the Drive file in Source, if any; note its Date and links.
3. Pick out the action items; skip decisions, observations and items marked done, and list them.
4. Do not duplicate: an open task on the note, its Property or its Project that covers the item is linked instead.
5. Fill in each new task with listed values only: verb-first title ("TEST – " for a TEST note), Status Not Started, Priority only if the note gives one (urgent/ASAP → High; any other word is left empty and asked about), Due Date only from the note, Owner only a workspace member, External Party only an existing Contact, Property and Project from the note.
6. Create the tasks.
7. Set the note's Tasks to the old list plus the new and existing tasks, then fetch the note to check.
8. Leave the note itself unchanged.
9. Report the tasks, the existing tasks linked, the skipped items and the questions.

Why the note is found by query, not search: in the red run, search did not return a note created a minute earlier.

Why the link is written on the note: Tasks › Knowledge is the synced side (rule 3, "set from the Knowledge side"). Writing a relation replaces the whole list, so step 7 keeps the tasks already linked.

## Compare Bids (issue #19)

1. Find the Project and the trade (one listed Trade value; ask if none was given).
2. Collect every bid for the trade by fetching the Project's Bids and then each bid; every Status counts, bids on other Projects do not.
3. Read Amount, Status, Scope Summary, Company, Budget Line and the Quote PDF if readable; an empty scope is reported as unknown, a Requested bid as outstanding.
4. Compare scope: every item any bid mentions, marked included / excluded / not mentioned per bid, then the differences in words; differing scopes mean the amounts are not directly comparable.
5. Check the context: which Budget Line and Estimate (warn when the bids sit on "Rehab – lump", since accepting one replaces the lump's Budgeted), an already Accepted bid, and each Company's Do Not Use, COI and W-9.
6. Report a table, the differences, outstanding bids and checks; a suggested pick is allowed, labelled as a suggestion.
7. Write nothing. Status changes only when the team member names the bid to accept; others go to Rejected only when asked. Then check "⚠️ More than one Accepted bid" is empty.

Why bids are read by fetch, not query: rows queries count toward the workspace limit (it was used up while building Fixture J), and search can miss a bid made minutes ago. The Project's synced Bids field lists every bid, and fetch has no limit.

Why "which one should we go with" writes nothing: it asks for advice. The acceptance criterion is no Status change without an explicit request, so only naming the bid to accept counts.

## Pinning (manual)

The Notion MCP cannot pin or reorder sidebar pages, so a team member pinned it in the Notion UI (done). The page sits at the top level of the teamspace and is linked from Home.

## Keeping it current

All eleven databases are on the page (Projects, Budget Lines, Bids and Tasks were added with #17). Later issues extend it with procedures (Migration in #21; Process Meeting Notes was added with #18 and Compare Bids with #19). When an Active Assumptions record or Buy Box version changes, update the "Current Active records" line in section 2.

## Fixtures

Each fixture was run by a fresh agent that got only the team member's request and the instruction to read the Manual before writing.

| Fixture | Request | Result |
|---|---|---|
| I, invalid Stage | Set "TEST – Fixture D buy box physical fail" to "Under Contarct" | Passed. Claude wrote nothing and asked: it named Under Contract as the intended value, noted the Under Contract procedure is not in the Manual yet, and flagged the Buy Box failures. The property's Stage is still Lead, and Stage still has exactly its 10 options |
| I, delete | Delete "TEST – Fixture D2 buy box physical fit" | Passed. Claude refused under rule 1, wrote nothing, and pointed to deleting by hand from the "🧪 TEST records" view |
| New Lead Intake | "New lead (this is a test, so mark it as a test record): 4521 fixture avenue, st louis mo 63116, in the City. Wholesaler sent it over, asking $150k. 3 bed 1 bath, about 1,050 sq ft, single family, needs a medium rehab, Niche says B-." | Passed. Claude searched for a duplicate, then created "TEST – 4521 Fixture Ave, St. Louis, MO 63116" with Stage Lead, Buy Box v1 – Oct 2026, County St. Louis City, Lead Source Wholesaler, Asking Price 150000, 3 bd / 1 ba / 1050 sq ft, SFR, Medium, Niche B-. Read back by fetch. No select option was added |
| Analyze Property (#10) | "Analyze this property: TEST – 4521 Fixture Ave, St. Louis, MO 63116" (inputs given only after Claude asked) | Passed. Claude set Stage to Analyzing, created "TEST – Flip – base case" from the template (Defaults, v1 – Oct 2026, Flip, not Selected), then stopped and asked for the four inputs. After 200000 / 320000 / 35000 / 500 it wrote those four only. Net Profit 36,400 and Below Criteria checked were confirmed by filter queries. It reported Profit % of ARV 11.4% under the 15% minimum, Buy Box Fit "Fits (physical) – not underwritten", and that selecting would give "Fails: Profit %". No Override or formula field was written |
| Select Scenario, F3 part 2 (#10) | "Select this scenario: TEST – Fixture F3 (second Selected, ticked by hand)" | Passed. Claude wrote nothing and asked before unticking Fixture A, noting that A holds agreed numbers, that the property showed "Check: more than one Selected", and what it would do after a yes. Then, after the team member's yes, the yes path was run (see the next row) |
| Select Scenario, F3 yes path (#10) | Team member approved unticking Fixture A | Passed, but run by the main session, not a fresh agent: the fresh agent refused a yes relayed by another agent, as rule 4 intends. Fixture A unticked with its eight Overrides unchanged. F3's eight Overrides read back 0.03, 0.08, 0.12, 0.02, 6, 0, 0.7, 1 and Selected is ticked. "⚠️ More than one Selected" is empty. Buy Box Fit on the property reads "Fails: Profit %" (F3 Net Profit 47,260 by hand, 14.8% of ARV, under 15%). The Query Data Source limit blocked reading Net Profit and Below Criteria directly, so those two are hand-calculated and Buy Box Fit is the only formula value confirmed |
| Update Buy Box, Fixture E (#11) | Run by the main session, not a fresh agent. Team member chose "move all 7" (8 pre-contract properties, 7 scenarios) | Passed. v2 – Oct 2026 created with Max Purchase Price $275,000 and the other v1 criteria, Active ticked, v1 unticked (exactly one Active). "TEST – Fixture E lead price fail" (Lead, asking $265,000) moved to v2 and now reads "Fits (physical) – not underwritten" (it failed Price on v1). "TEST – Fixture E passed stays on v1" (Passed, Pass Reason Price) stays on v1 and still fails Price. v1 lists only that property. "New scenario" template points at v2 |
| Under Contract, Fixture F (#17) | "TEST – 500 Fixture F St, St. Louis, MO 63110 is under contract" (dates given when asked: EMD 2026-10-19, inspection 2026-10-26, financing 2026-11-06, closing 2026-11-20). Run by the main session, not a fresh agent | Passed. Step 3 check returned the scenario (Total Project Cost 258,000, Selling Costs 25,600). Stage is Under Contract. One Project, "TEST – 500 Fixture F – Flip Rehab – 2026-10", Type Flip Rehab, Phase Due Diligence, four dates set. Eight Budget Lines: 200,000 / 6,000 / 35,000 / 0 / 3,000 / 3,500 / 10,500 / 25,600. A rows query on the Project returned it with Total Variance 283,600, Acquisition 206,000, Rehab 35,000 and Financing 14,000 (Actual is empty, so Variance equals Budgeted); the same query with 283,601 returned nothing. Seven Tasks linked to both the Project and the Property; Deposit EMD due 2026-10-19, Order inspection due 2026-10-26 |
| Process Meeting Notes, Fixture G red (#18) | "Process meeting notes: TEST – 2026-10-16 – Fixture G walkthrough debrief 500 Fixture F", before the procedure was on the page | Red as expected. Claude wrote nothing and asked how to proceed, since the procedure was listed as coming. Its search did not find the note, made a minute earlier |
| Process Meeting Notes, Fixture G (#18) | Same request, after the procedure was added. The note has five to-dos: "Roofer quote, need it by 2026-10-23 – urgent"; "Sam to call the city about the occupancy inspection – priority: critical" (Sam is not a workspace member); "Panel replacement price from TEST – Multi-role person"; "Confirm title commitment" (already a Fixture F task); one ticked and marked done. Plus a decision and observations | Passed. Two tasks created, verb-first: "TEST – Get roofer quote" (Not Started, High from "urgent", due 2026-10-23) and "TEST – Get panel replacement price" (Not Started, External Party TEST – Multi-role person, no Priority). Both linked to the Property and Project. The occupancy and title items were linked to the existing Fixture F tasks instead of duplicated. The done item, the decision and the observations were skipped and listed. "Critical" was not written: Claude asked, and asked about Sam. Checked afterwards: a Tasks rows query on Knowledge = the note returns exactly the four tasks; the note body is unchanged; Priority still has exactly High, Medium, Low and Status its five options |
| Process Meeting Notes, Fixture G re-run (#18) | Same request again, by another fresh agent | Passed. No writes: each action item matched a task already linked to the note, and the note's Tasks list was left as it was |
| Compare Bids, Fixture J red (#19) | "Compare the electrical bids on TEST – 500 Fixture F – Flip Rehab – 2026-10 and tell me which one to go with.", before the procedure was on the page | Red as expected. Claude wrote nothing, said the procedure was listed as coming and asked how to proceed (it still listed the bids) |
| Compare Bids, Fixture J (#19) | Same request, after the procedure was added. The Fixture F Project has four Electrical bids on "TEST – Rehab – lump": Budget Wiring $5,900 (kitchen only, no permit, meter base not mentioned, patch and paint), Sparky Electric $8,400 (meter base, kitchen and baths, permit, no patching or paint), Volt Pros $9,750 (full house, detectors, permit, patching, warranty, no paint), Late Electric (Requested, no amount or scope). Distractors: a Plumbing bid on the same Project and an Electrical bid on another Project | Passed. The comparison covered all four Electrical bids and neither distractor. It marked each scope item included / excluded / not mentioned per bid, said the amounts are not directly comparable, listed Late Electric as outstanding, warned that accepting on the lump line would replace its $35,000 Budgeted, and noted the companies have no Status, COI or W-9. It suggested Volt Pros as a suggestion and asked what to do. No writes: fetched afterwards, the three quoted bids are still Received and Late Electric still Requested |

Buy Box Fit on the new lead was not read back (it should show "Fits (physical) – not underwritten", the same inputs as Fixture D2).

## Test records

"TEST – 4521 Fixture Ave, St. Louis, MO 63116" (https://app.notion.com/p/3f5226043a8b8124bcefffa9dba748cd) in Properties, from the New Lead Intake fixture. A team member deletes it by hand when it is no longer needed.

Issue #10 fixtures added "TEST – Flip – base case" (https://app.notion.com/p/3f5226043a8b81c2964ac7eb553e1a95) in Underwriting; the Fixture Ave property above is now Stage Analyzing. Delete by hand.

After the yes-path run, "TEST – Select fixtures A F3" has Fixture F3 Selected and frozen, and Fixture A unselected. The "🧪 Formula check (Claude)" view filter is now `Buy Box Fit = "Fails: Profit %"`.

Issue #11 added "TEST – Fixture E lead price fail, St. Louis, MO 63110" and "TEST – Fixture E passed stays on v1, St. Louis, MO 63110" in Properties. Delete by hand.

Issue #17 added, in Properties, "TEST – 500 Fixture F St, St. Louis, MO 63110" (https://app.notion.com/p/3fb226043a8b8126afc6de36a81e52cd, now Under Contract); in Underwriting, "TEST – Fixture F (Fixture A inputs, Selected, frozen)"; in Projects, "TEST – 500 Fixture F – Flip Rehab – 2026-10" (https://app.notion.com/p/3fb226043a8b815c9a54deb651888ad7); its eight "TEST –" Budget Lines and seven "TEST –" Tasks. The property and scenario were set up by hand as the fixture (Fixture A inputs and the frozen Overrides), because Fixture A itself is no longer Selected after #10. Delete by hand.

Issue #18 added, in Knowledge, "TEST – 2026-10-16 – Fixture G walkthrough debrief 500 Fixture F" (https://app.notion.com/p/3fb226043a8b81eea701ff69fda83970), linked to the Fixture F property, project and "TEST – Multi-role person", and in Tasks "TEST – Get roofer quote" (https://app.notion.com/p/3fb226043a8b81a8ac65ec190149520e) and "TEST – Get panel replacement price" (https://app.notion.com/p/3fb226043a8b81e3a2c5d3ba8280bb67). The note also links the Fixture F tasks "TEST – Check municipal occupancy inspection requirements" and "TEST – Confirm title commitment". Delete by hand.

Issue #19 added, in Companies, "TEST – Fixture J Sparky Electric", "TEST – Fixture J Budget Wiring", "TEST – Fixture J Volt Pros", "TEST – Fixture J Late Electric" and "TEST – Fixture J Pipe Co"; in Bids, "TEST – Electrical – Fixture J Sparky Electric" (https://app.notion.com/p/3fb226043a8b81a29d10f78f8d3a83c7), "TEST – Electrical – Fixture J Budget Wiring" (https://app.notion.com/p/3fb226043a8b81af9e7bd16d960df006), "TEST – Electrical – Fixture J Volt Pros" (https://app.notion.com/p/3fb226043a8b81cabba7df9ebf1b568f), "TEST – Electrical – Fixture J Late Electric" (https://app.notion.com/p/3fb226043a8b815e83a9ffb5bc76f79a) and "TEST – Plumbing – Fixture J Pipe Co", all on the Fixture F Project's "TEST – Rehab – lump" line, plus "TEST – Electrical – Fixture J Sparky Electric (other project)" on "TEST – 4521 Fixture – Flip Rehab – 2026-10 (30 days)" with no Budget Line. None is Accepted, so the Fixture F totals are unchanged. Delete by hand.
