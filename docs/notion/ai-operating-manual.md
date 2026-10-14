# AI Operating Manual

Built for issue #9. A top-level page in the "REI Operating System" teamspace, linked first under System on Home.

- Page: https://app.notion.com/p/3f5226043a8b81e38edbd281ec1c8390

Claude reads it before it writes anything in the teamspace. The Notion page is the source of truth for Claude; this file records what it holds and how it was checked.

## Sections

| # | Section | Holds |
|---|---|---|
| 1 | Rules | Never delete or archive; never create a new select option; write inputs only; never change a Selected scenario's numbers without approval; do not change the schema; ask when unsure; run procedures only when asked |
| 2 | Database map | The seven databases built so far (Knowledge added in #16), what one record is, and every relation with its synced side; the current Active Assumptions and Buy Box records |
| 3 | Field dictionary | Every field, its type and the exact allowed values, per database, including Knowledge and the glossary pointer (#16). Formula, roll-up and synced fields are marked read-only |
| 4 | Formulas | What each Underwriting result and Buy Box Fit means, and how to read formula values through the MCP |
| 5 | Naming rules | Property, scenario, Buy Box version, Contact, Company, Project, Task, Knowledge and TEST record titles |
| 6 | Procedures | New Lead Intake, Analyze Property, Select Scenario, Update Buy Box, Answer from Knowledge. The other procedures are listed as coming |
| 7 | Data-quality views | The views that should be empty |

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

## Pinning (manual)

The Notion MCP cannot pin or reorder sidebar pages, so a team member pinned it in the Notion UI (done). The page sits at the top level of the teamspace and is linked from Home.

## Keeping it current

Later issues extend the page when they add databases (Projects, Budget Lines, Bids, Tasks; Knowledge was added in #16) and procedures (Under Contract in #17, Process Meeting Notes in #18, Compare Bids in #19, Migration in #21). When an Active Assumptions record or Buy Box version changes, update the "Current Active records" line in section 2.

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

Buy Box Fit on the new lead was not read back (it should show "Fits (physical) – not underwritten", the same inputs as Fixture D2).

## Test records

"TEST – 4521 Fixture Ave, St. Louis, MO 63116" (https://app.notion.com/p/3f5226043a8b8124bcefffa9dba748cd) in Properties, from the New Lead Intake fixture. A team member deletes it by hand when it is no longer needed.

Issue #10 fixtures added "TEST – Flip – base case" (https://app.notion.com/p/3f5226043a8b81c2964ac7eb553e1a95) in Underwriting; the Fixture Ave property above is now Stage Analyzing. Delete by hand.

After the yes-path run, "TEST – Select fixtures A F3" has Fixture F3 Selected and frozen, and Fixture A unselected. The "🧪 Formula check (Claude)" view filter is now `Buy Box Fit = "Fails: Profit %"`.

Issue #11 added "TEST – Fixture E lead price fail, St. Louis, MO 63110" and "TEST – Fixture E passed stays on v1, St. Louis, MO 63110" in Properties. Delete by hand.
