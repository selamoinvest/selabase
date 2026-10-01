# Spec: REI Notion Operating System (St. Louis Flips)

- **Status:** ready-for-agent
- **Date:** 2026-10-01
- **Source:** Design grilling session (5 rounds), confirmed by the user; follow-up grilling (3 rounds, 2026-10-01) resolved the open assumptions, buy box edge cases, scenario freezing, budget/bid behavior and test cleanup
- **Storage:** Local only (not published to an issue tracker, by user request)

---

## Problem Statement

We are a team of 3 people who buy, renovate and sell houses (flips) in the St. Louis, Missouri metro area. Our data is in a Notion workspace that is not organized. Properties, numbers, tasks, contacts and notes are in different places with no structure.

Because of this:

- We cannot see quickly which properties are in which stage, and why we passed on a property.
- Our deal numbers are not consistent. Each person calculates profit in a different way, with different assumptions.
- We cannot quickly check a property against our buy box.
- After we sign a contract, the critical deadlines (inspection period, earnest money, financing, closing, loan maturity) are not tracked in one place.
- We have no structure to compare budget against actual cost, to compare contractor bids, or to track lien waivers and contractor insurance.
- Our AI agent (Claude, connected through the Notion MCP) cannot work reliably in the workspace. It does not know the structure, the allowed values or the procedures, so it can create bad data.

## Solution

A new, structured Notion teamspace called **"REI Operating System"**. It has 11 linked databases, a Home dashboard and a pinned **AI Operating Manual** page.

- A **Property** is a permanent record of a physical house. It moves through a pipeline from Lead to Sold.
- **Underwriting** scenarios analyze a property. Notion formulas do all calculations. Each scenario shows a **Base** result (hard numbers only) and a **Fully loaded** result (with all assumptions). Each assumption has a central default and an optional override per scenario.
- A versioned **Buy Box** automatically tells us whether a property fits our criteria and, if not, which criteria fail.
- When a property goes **Under Contract**, a **Project** starts. The Project holds the milestone dates, the hard money loan data, the budget lines, the contractor bids and the tasks.
- **Tasks**, **Contacts/Companies (CRM)** and a **Knowledge Base** link to properties and projects.
- Claude reads the AI Operating Manual before it writes data. It follows a fixed vocabulary and documented procedures, and it never deletes records.
- Existing data is inventoried, mapped, migrated after approval and archived (never deleted).

## User Stories

### Pipeline and properties

1. As a team member, I want each property to be one record titled with its full address, so that people and Claude find it without ambiguity and can match county and title records.
2. As a team member, I want a `Stage` for each property (Lead, Analyzing, Walkthrough Scheduled, Offer Submitted, Negotiating, Under Contract, Owned, Sold, Passed, Lost), so that I see where every deal is.
3. As a team member, I want a pipeline board grouped by Stage, so that I see the full deal flow at a glance.
4. As a team member, I want to record a `Pass Reason` when a property is Passed or Lost, so that we learn why deals die.
5. As a team member, I want a view that shows Passed/Lost properties with no Pass Reason, so that we fix missing data.
6. As a team member, I want to record the County, Municipality, Parcel ID/Locator #, Zoning and Flood Zone, so that I know which local rules apply.
7. As a team member, I want to record Beds, Baths, Sq Ft, Year Built and Property Type, so that I can check the property against the buy box.
8. As a team member, I want to record the Asking Price, Condition and Lead Source, so that I can measure which lead sources give good deals.
9. As a team member, I want to record the Niche neighborhood grade, so that neighborhood class is measured the same way by all of us.
10. As a team member, I want to attach photos and documents to a property, so that walkthrough evidence stays with the property.
11. As a team member, I want the property to stay as a permanent record after we buy it, so that its full history (analysis, project, sale) is in one place.
12. As a team member, I want rental fields to exist but be hidden, so that we can start rentals later without a schema rebuild.

### Buy Box

13. As a team member, I want the buy box to be a record that I edit in one place, so that a change updates all properties.
14. As a team member, I want buy box versions (for example "v1 – Oct 2026") with one marked Active, so that I keep history of past criteria.
15. As a team member, I want each property to show `Buy Box Fit` (Fits / Fails with the list of failed criteria), so that I see immediately why a property does not fit.
15a. As a team member, I want a property with no Selected scenario to show "Fits (physical) – not underwritten" when it passes the physical criteria, so that an unanalyzed lead neither looks failed nor looks fully approved.
16. As a team member, I want the buy box to check purchase price ≤ $250k, scope of work ≤ $40k, Niche grade in {C+, B-, B}, beds ≥ 3, baths ≥ 1, and sq ft ≥ 800, so that we screen deals against our real criteria.
17. As a team member, I want the buy box to also check return thresholds (Fully loaded net profit ≥ $30k and ≥ 15% of ARV), so that a property that fits the physical criteria but has poor returns is flagged.
18. As a team member, I want to create a new buy box version and make it Active without editing formulas, so that updating the buy box is easy.
18a. As a team member, I want only pre-contract properties (Lead, Analyzing, Walkthrough Scheduled, Offer Submitted, Negotiating) to move to a new buy box version, so that Passed, Lost, Owned and Sold properties keep the version they were judged against.

### Underwriting

19. As a team member, I want to create several underwriting scenarios for one property (for example "Flip – conservative ARV"), so that I can compare options.
20. As a team member, I want to mark one scenario as `Selected`, so that the team knows which numbers we act on.
20a. As a team member, I want a scenario's assumptions to freeze when it is marked Selected (every effective value is copied into its override field), so that a later change to a central default does not silently change agreed numbers.
20b. As a team member, I want only one Selected scenario per property, and a data-quality view that shows properties with more than one, so that the buy box and the Under Contract procedure read one set of numbers.
21. As a team member, I want to enter only inputs (purchase price, ARV, rehab estimate, holding cost per month), so that Notion formulas calculate the results the same way every time.
22. As a team member, I want to see the Base result (ARV minus purchase, buy closing and rehab), so that I see the deal without soft assumptions.
23. As a team member, I want to see the Fully loaded result (with hard money interest and points, holding, selling costs and rehab contingency), so that I see the realistic profit.
24. As a team member, I want to see the Maximum Allowable Offer (70% rule), so that I know the highest price to offer.
25. As a team member, I want to see Total Project Cost, Net Profit, Cash Invested and ROI, so that I can compare deals.
26. As a team member, I want each assumption to show its default value from the central Assumptions record, so that I know what the formula uses.
27. As a team member, I want to override any assumption on one scenario, so that I can model a specific deal (for example a real lender quote).
28. As a team member, I want the formula to use the override if it is filled in and the default if it is empty, so that overrides are explicit.
29. As a team member, I want a change to a central default to update all scenarios that have no override (Selected scenarios are frozen, see 20a), so that I update assumptions in one place.
30. As a team member, I want each scenario to show whether it is `Below Criteria`, so that weak deals are flagged.

### Assumptions

31. As a team member, I want one central Assumptions record with: buy closing 3%, selling costs 8% of ARV, hard money rate 12%, points 2%, hold period 6 months, rehab contingency 0%, loan-to-cost 70% of purchase and 100% of rehab, so that every scenario starts from the same defaults.
32. As a team member, I want to update the hard money terms when we get a real lender quote, so that the defaults reflect our real financing.

### Projects (after contract)

33. As a team member, I want a Project to start when a property goes Under Contract, so that the contingency period is tracked.
34. As a team member, I want the Project title to be "[short address] – [type] – [YYYY-MM]", so that it is easy to search.
35. As a team member, I want a Project `Type` (Flip Rehab, BRRRR Rehab, Rental Turnover, CapEx Repair), so that the model supports future project types.
36. As a team member, I want a Project `Phase` (Due Diligence, Closing, Pre-Construction, Rehab, Punch List, Listed / Lease-Up, Sold / Refinanced / Stabilized, Closed Out, Terminated), so that I see where every project is.
37. As a team member, I want milestone date fields on the Project (EMD Due, Inspection Deadline, Appraisal Date, Financing Contingency Deadline, Closing Date, Rehab Start, Target Rehab Complete, Actual Rehab Complete, Occupancy Inspection, List Date, Sale Closed, Loan Maturity Date), so that all deadlines are in one place.
38. As a team member, I want a "deadlines in the next 14 days" view across all projects, so that we never miss a contingency or loan maturity.
39. As a team member, I want occupancy inspection fields (Required?, Status: Scheduled / Passed / Failed / Re-inspect, Escrow Amount), so that we handle St. Louis City and County municipal requirements.
40. As a team member, I want hard money loan fields (Lender, Loan Amount, Rate, Points, Term, Maturity Date, Extension Fee, Rehab Holdback), so that we track the loan and avoid unplanned extensions.
41. As a team member, I want Project roll-ups of total Budgeted vs. Actual, by cost group, so that I see overruns early.

### Budget lines

42. As a team member, I want budget lines grouped by Cost Group (Acquisition, Rehab, Holding, Financing, Selling), so that I see the full cost of the project.
43. As a team member, I want standard rehab categories (Demo, Roof, HVAC, Electrical, Plumbing, Kitchen, Bath, Flooring, Paint, Exterior, Permits, Contingency), so that budgets are comparable between projects.
44. As a team member, I want an Estimate, a Budgeted and an Actual amount per line, so that I see the variance. Budgeted is the accepted bid amount if one exists, else the Estimate.
45. As a team member, I want to link each line to a vendor (Company/Contact), so that I know who was paid.
46. As a team member, I want a Draw # and draw dates (Requested, Inspected, Funded) per line, so that I track hard money draws.
47. As a team member, I want a `Lien Waiver Received` checkbox per paid line, so that we are protected against mechanic's liens in Missouri.
48. As a team member, I want a view of paid lines with no lien waiver, so that we chase missing waivers before final payment.
49. As a team member, I want to attach receipts to lines, so that we have proof of cost.

### Bids

50. As a team member, I want a Bids database (Project, Company, Trade, Amount, Scope Summary, Quote PDF, Status: Requested / Received / Accepted / Rejected), so that I compare contractor quotes.
51. As a team member, I want the Budgeted amount of a budget line to be a formula that reads its accepted bid, so that the budget reflects real quotes without anyone copying numbers.
52. As a team member, I want Claude to compare bids for one trade and point out differences in scope, so that we choose correctly.

### Tasks

53. As a team member, I want one Tasks database for general, property and project tasks, so that there is one task list.
54. As a team member, I want task fields Status (Not Started, In Progress, Waiting on External, Done, Cancelled), Priority (High, Medium, Low), Due Date, so that tasks are clear.
55. As a team member, I want an `Owner` (Notion person) on each task, so that I get a "My Tasks" view and notifications.
56. As a team member, I want an `External Party` (link to Contacts) on each task, so that we know which contractor or agent we wait on.
57. As a team member, I want tasks to link to Property, Project and Knowledge, so that each task has context.
58. As a team member, I want task titles to start with a verb, so that they are actionable.
59. As a team member, I want recurring tasks to use Notion repeat templates, so that Claude does not need to recreate them.

### CRM

60. As a team member, I want one Contacts database with multi-select `Roles` (Contractor, Agent, Lender, Title, Inspector, Appraiser, Insurance, Attorney, Property Manager, Wholesaler, Hire Candidate), so that one person with many roles is one record.
61. As a team member, I want contact fields Phone, Email, Company and Source/Referred By, so that I know how to reach people and where they came from.
62. As a team member, I want a Companies database with Trades, License #, COI Expiration, W-9 on File, Status (Prospect, Vetted, Preferred, Do Not Use) and Rating, so that I vet contractors.
63. As a team member, I want a view of companies whose COI expires within 30 days, so that we do not pay an uninsured contractor.
64. As a team member, I want a view of paid vendors with no W-9, so that we can issue 1099s in January.
65. As a team member, I want a `Hiring Stage` for candidates (Sourced, Screening, Interview, Trial, Hired, Rejected), so that we track potential hires.

### Knowledge Base

66. As a team member, I want one Knowledge database with `Type` (Meeting Note, Course/Lesson, Glossary Term, SOP, Market Research), so that all knowledge is in one place.
67. As a team member, I want knowledge entries to link to Properties, Projects and Contacts, so that a meeting note shows on the property it discusses.
68. As a team member, I want glossary entries for terms like ARV, MAO, LTC, EMD and COI, so that the team and Claude use the same definitions.

### Home dashboard and layout

69. As a team member, I want a Home dashboard with active projects, deadlines in the next 14 days, my tasks, pipeline by stage and COIs expiring within 30 days, so that I start each day in one place.
70. As a team member, I want one page per pillar and all databases inside one "Databases" page, so that the workspace is easy to navigate.

### AI agent (Claude through the Notion MCP)

71. As Claude, I want an AI Operating Manual page with every database, field, type, allowed value and relation, so that I write valid data.
72. As Claude, I want documented procedures, so that I do multi-step work the same way every time.
73. As a team member, I want Claude to read the AI Operating Manual before it writes data, so that it does not create wrong select options.
74. As a team member, I want Claude never to delete or archive records, so that no data is lost.
75. As a team member, I want Claude to write inputs only and never write into formula fields, so that calculations stay deterministic.
76. As a team member, I want Claude to ask before it changes the numbers on a Selected scenario, so that agreed numbers do not change silently.
77. As a team member, I want Claude to use its own Notion account (company email, 4th seat), so that page history shows which changes Claude made.
78. As a team member, I want to say "this property is under contract" and have Claude run the Under Contract procedure, so that the handoff is complete.
79. As a team member, I want the Under Contract procedure to create the Project, create budget lines from the Selected scenario, and create due-diligence tasks (order inspection, order appraisal, confirm title commitment, deposit EMD, get insurance quote, check municipal occupancy inspection requirements, get contractor quotes), so that nothing is missed.
80. As a team member, I want to ask Claude to "process meeting notes" and get tasks with links back to the note, so that action items are not lost.
81. As a team member, I want Claude to do a New Lead Intake (create the property with the correct title, fields and Stage = Lead), so that leads enter the system correctly.
82. As a team member, I want Claude to tell me when a property fails the buy box or a scenario is Below Criteria, so that I focus on good deals.
82a. As a team member, I want to say "analyze this property" and have Claude run the Analyze Property procedure (create a scenario linked to the property, ask for the inputs, set Stage = Analyzing), and to say "select this scenario" to have Claude freeze it and untick any other Selected scenario on the property, so that analysis is done the same way every time.

### Migration

83. As a team member, I want Claude to inventory the old workspace and propose a mapping to the new databases, so that I approve before data moves.
84. As a team member, I want Claude to migrate data only after I approve the mapping, so that nothing is moved by mistake.
85. As a team member, I want old pages moved to an "Archive – Legacy" page (not deleted), so that we can go back to the original data.

## Implementation Decisions

### Platform and access

- The system is one Notion teamspace named "REI Operating System".
- Claude connects through Notion's hosted MCP server, using a dedicated Notion member account with the company email (4th seat). This is the same email as the company Claude account. That is acceptable because the two logins are independent.
- The company-email Notion account creates the workspace and is an Owner. At least one team member is also a Workspace Owner, as a recovery path.
- No person works in Notion as the company-email account, so page history stays clean.
- In the Claude connector settings, the Notion write tools start as "Needs approval". This is a recommended setting, not a hard requirement.

### Databases (11) and relations

| Database | Purpose | Key relations |
|---|---|---|
| Properties | Permanent record of a physical house | Underwriting (1:many), Projects (1:many), Tasks, Knowledge, Buy Box (many:1, Active version) |
| Underwriting | Analysis scenarios | Property (many:1), Assumptions (many:1) |
| Assumptions | Central default values (one active record) | Underwriting (1:many) |
| Buy Box | Versioned screening criteria (one Active) | Properties (1:many) |
| Projects | Post-contract work on one property | Property (many:1), Budget Lines, Bids, Tasks, Knowledge, Lender (Companies) |
| Budget Lines | Budgeted vs. actual cost lines | Project (many:1), Vendor (Companies/Contacts), Bid (accepted) |
| Bids | Contractor quotes | Project, Company, Budget Line |
| Tasks | All tasks | Property, Project, Knowledge, External Party (Contacts) |
| Contacts | People | Company, Tasks, Knowledge |
| Companies | Businesses (contractors, lenders, title, etc.) | Contacts, Bids, Budget Lines, Projects (as lender) |
| Knowledge | Notes, courses, glossary, SOPs, research | Properties, Projects, Contacts, Tasks |

### Naming conventions

- Property title: full address, for example "1234 Main St, St. Louis, MO 63110".
- Project title: "[short address] – [Type] – [YYYY-MM]", for example "1234 Main – Flip Rehab – 2026-11".
- Task title: starts with a verb.
- Emojis are allowed in titles and icons.

### Properties schema

- Location: Address (title), County (select: St. Louis City, St. Louis County, St. Charles, Jefferson, Franklin), Municipality, Parcel ID / Locator #, Zoning, Flood Zone.
- Physical: Beds, Baths, Sq Ft, Year Built, Property Type (SFR, 2–4 Unit, Condo).
- Deal: Stage, Pass Reason (Price, Rehab Too Heavy, Location, Title, Lost to Other Buyer), Asking Price, Condition (Turnkey, Light, Medium, Heavy, Gut), Lead Source (MLS, Wholesaler, Direct Mail, Driving for Dollars, LRA, Tax Sale, Referral), Niche Grade (A+ to F).
- Media: Photos, Documents.
- Rental (hidden): Market Rent, Actual Rent, Lease End, Tenant Name.
- Computed: Buy Box Fit (formula).
- Notion cannot make a field mandatory. "Pass Reason required" is enforced by a data-quality view and by the AI Operating Manual.

### Underwriting schema and formulas

- Inputs: Purchase Price, ARV, Rehab Estimate, Monthly Holding Cost (taxes, insurance, utilities; per property, no default), Exit Strategy (Flip active; Rental hidden), Selected (checkbox).
- For each assumption: an Override field on the scenario. A formula field gives the Effective value: override if filled in, else the default from the linked Assumptions record.
- **Freeze on select:** when a scenario is marked Selected, every Effective value is copied into its Override field. After that, central default changes do not affect it. Unticking Selected does not clear the overrides; clearing them is a deliberate manual action.
- **One Selected per property:** Notion cannot enforce this. Property has a roll-up that counts Selected scenarios, and a data-quality view shows properties where the count is more than 1. When Claude selects a scenario it unticks the old one.
- Results, Base: Base Profit = ARV − Purchase − Buy Closing − Rehab.
- Results, Fully loaded:
  - Loan Amount = LTC on purchase × Purchase + LTC on rehab × Rehab
  - Points = Loan Amount × Points %
  - Interest = Loan Amount × Rate × Hold Months ÷ 12 (interest-only, full balance; a simplification)
  - Holding = Monthly Holding Cost × Hold Months
  - Contingency = Rehab × Contingency %
  - Total Project Cost = Purchase + Buy Closing + Rehab + Contingency + Holding + Points + Interest
  - Selling Costs = ARV × Selling %
  - Net Profit = ARV − Selling Costs − Total Project Cost
  - Cash Invested = Total Project Cost − Loan Amount
  - ROI = Net Profit ÷ Cash Invested
- MAO = ARV × 70% − Rehab.
- Below Criteria = Fully loaded Net Profit is below the Active buy box minimum profit, or below its minimum profit as a % of ARV.
- Rental formulas (NOI, cap rate, cash flow, cash-on-cash, DSCR) and refinance formulas are not built now.
- Formulas use Notion formulas 2.0, which read values through relations. Database templates prefill the relation to the active Assumptions record and the Active Buy Box.

### Assumptions defaults (central record)

| Assumption | Default | Status |
|---|---|---|
| Buy closing costs | 3% of purchase | Confirmed |
| Selling costs | 8% of ARV | Confirmed |
| Hard money rate | 12% annual, interest-only | Confirmed (replace with real quote) |
| Hard money points | 2% | Confirmed (replace with real quote) |
| Hold period | 6 months | Confirmed |
| Rehab contingency | 0% of rehab (field kept; override per scenario, for example on a Gut rehab) | Confirmed |
| Loan-to-cost on purchase | 70% | Confirmed (may change with lender) |
| Loan-to-cost on rehab | 100% | Confirmed |

### Buy Box schema (versioned)

- Fields: Version Name, Active (checkbox, only one), Max Purchase Price ($250k), Max Scope of Work ($40k), Allowed Niche Grades (C+, B-, B), Min Beds (3), Min Baths (1), Min Sq Ft (800), Min Net Profit ($30k), Min Profit % of ARV (15%).
- Buy Box Fit on Property shows "Fits", or "Fails:" followed by the list of failed criteria (Price, SOW, Niche, Beds, Baths, Sq Ft, Profit, Profit %).
- **With a Selected scenario:** all criteria are checked. Price uses the scenario Purchase Price, SOW uses the scenario Rehab Estimate, Profit and Profit % use the Fully loaded Net Profit.
- **Without a Selected scenario:** only the physical criteria are checked (Price against Asking Price, Niche, Beds, Baths, Sq Ft). SOW, Profit and Profit % are skipped. The result is "Fits (physical) – not underwritten", or "Fails:" followed by the failed physical criteria.
- To change the buy box, create a new version, mark it Active, unmark the old one, and point only pre-contract properties (Lead, Analyzing, Walkthrough Scheduled, Offer Submitted, Negotiating) at the new version. Passed, Lost, Under Contract, Owned and Sold properties keep their version. Claude can do this as the "Update Buy Box" procedure.

### Projects schema

- Type, Phase, Property, milestone date fields (see user story 37), occupancy inspection fields (Required?, Status, Escrow Amount).
- Hard money loan: Lender (Company), Loan Amount, Rate, Points, Term, Maturity Date, Extension Fee, Rehab Holdback.
- Roll-ups: Budgeted and Actual totals per Cost Group, and the variance.
- Loan and draw fields are provisional. They will be reviewed on the first real project.

### Budget Lines schema

- Cost Group (Acquisition, Rehab, Holding, Financing, Selling), Category (rehab categories as listed), Estimate (input), Budgeted (formula), Actual, Variance (formula), Vendor, Draw #, Draw Requested / Inspected / Funded dates, Lien Waiver Received, Receipt.
- Budgeted = amount of the linked Bid with Status Accepted if one exists, else Estimate. Nobody types into Budgeted.

### Other schemas

- Bids, Tasks, Contacts, Companies and Knowledge are as described in the user stories.

### AI Operating Manual (pinned page)

1. Database map and relations.
2. Field dictionary: every field, type and the exact allowed values.
3. Formula meanings, plus the rule "write inputs only".
4. Naming rules.
5. Procedures: New Lead Intake, Analyze Property, Select Scenario, Under Contract, Process Meeting Notes, Update Buy Box, Compare Bids, Migration.
6. Rules: never delete or archive; never create a new select option, ask instead; never change the numbers on a Selected scenario without approval; ask when unsure.

### Analyze Property procedure

1. Set Property Stage to Analyzing.
2. Create an Underwriting scenario linked to the Property, the active Assumptions record and the Active Buy Box (the database template prefills the last two).
3. Ask the user for Purchase Price, ARV, Rehab Estimate and Monthly Holding Cost, and write them. Write inputs only.
4. Report the results, Below Criteria, and what Buy Box Fit will show if this scenario is selected.

### Select Scenario procedure

1. If another scenario on the same Property is Selected, ask before unticking it (it holds agreed numbers).
2. Copy every Effective assumption value into its Override field (freeze).
3. Tick Selected. Report the Property's Buy Box Fit.

### Under Contract procedure

1. Set Property Stage to Under Contract.
2. Create the Project (Type, Phase = Due Diligence, title per convention, link to the Property).
3. Create Budget Lines from the Selected scenario, with the amounts in Estimate: Purchase and Buy Closing (Acquisition); Rehab as one lump line (Rehab, split into categories later as bids arrive); Contingency (Rehab); Holding (Holding); Points and Interest (Financing); Selling Costs (Selling). The Budgeted total equals Total Project Cost + Selling Costs (= ARV − Net Profit).
4. Create the due-diligence tasks: order inspection, order appraisal, confirm title commitment, deposit EMD, get insurance quote, check municipal occupancy inspection requirements, get contractor quotes.
5. Ask the user for the contract dates (EMD, inspection, financing, closing) and fill the milestone fields.

### Migration

- Inventory the old workspace, propose a mapping, get approval, migrate, then move the old pages to "Archive – Legacy". Nothing is deleted.

## Testing Decisions

- **One seam:** the Notion workspace as seen through the Notion MCP, which is the same interface Claude uses. Tests check external behavior only (field values that a user or Claude reads), not how Notion formulas are written internally.
- A good test creates records with known inputs, reads the calculated values back, and compares them to expected values calculated by hand.
- Test records use the prefix "TEST –". After the run, a team member deletes them by hand in the Notion UI, using a view filtered on the "TEST –" prefix. Claude never deletes, so the no-delete rule stays absolute.
- There is no prior art. This is a new Notion system, not a code repository.

### Acceptance scenarios

**Fixture A, flip scenario with default assumptions, Selected:** Purchase $200,000, ARV $320,000, Rehab $35,000, Monthly Holding $500, Niche B, 3 beds, 1 bath, 1,100 sq ft. Defaults as in the Assumptions table (LTC 70% purchase / 100% rehab, contingency 0%). Run Select Scenario on it.

| Result | Expected |
|---|---|
| Buy Closing | $6,000 |
| Base Profit | $79,000 |
| Loan Amount | $175,000 |
| Points | $3,500 |
| Interest | $10,500 |
| Holding | $3,000 |
| Contingency | $0 |
| Total Project Cost | $258,000 |
| Selling Costs | $25,600 |
| Net Profit (Fully loaded) | $36,400 |
| Cash Invested | $83,000 |
| ROI | ≈ 43.9% |
| MAO | $189,000 |
| Below Criteria | Yes (profit ≥ $30k, but 11.4% < 15% of ARV, which is $48,000) |
| Buy Box Fit | Fails: Profit % |
| Overrides after select | All filled with the effective defaults (frozen) |

**Fixture A2, same inputs, not Selected:** A second scenario on another TEST property with the same inputs as A, not Selected. Expected: same results as A, and no overrides filled.

**Fixture B, override:** Same as A2, with the Selling override set to 6%. Expected: Selling Costs $19,200, Net Profit $42,800 (13.4% of ARV), Below Criteria Yes.

**Fixture C, default change:** Change the central Selling default to 7%. Expected: A2's Net Profit changes to $39,600. A (Selected, frozen) stays at $36,400. B (override) stays at $42,800. Then restore 8%.

**Fixture D, buy box physical failure, not underwritten:** Asking $260,000, 2 beds, 1 bath, 1,000 sq ft, Niche C, no scenario. Expected: Buy Box Fit "Fails: Price, Niche, Beds". SOW, Profit and Profit % are not listed.

**Fixture D2, not underwritten, physical fit:** Asking $180,000, 3 beds, 1 bath, 1,000 sq ft, Niche B-, no scenario. Expected: Buy Box Fit "Fits (physical) – not underwritten".

**Fixture E, buy box versioning:** Create v2 with Max Purchase $275,000 and make it Active, then run Update Buy Box. Expected: D (Stage = Lead) points at v2 and no longer fails on Price. A Passed TEST property stays on v1. v1 stays as history.

**Fixture F, Under Contract procedure:** Run the procedure on Fixture A. Expected: Stage = Under Contract, one Project with the correct title and Phase = Due Diligence, budget lines per the procedure whose Budgeted total is $283,600 (Total Project Cost $258,000 + Selling Costs $25,600 = ARV − Net Profit), all 7 due-diligence tasks linked to the Project and the Property.

**Fixture F2, accepted bid sets Budgeted:** On the Fixture F Project, split a $12,000 Roof line out of the rehab lump (Estimate $12,000). Add two Roof bids ($11,000 and $13,500) linked to that line and mark the $11,000 bid Accepted. Expected: the Roof line's Budgeted shows $11,000 with no manual edit. Mark it Rejected again: Budgeted goes back to $12,000.

**Fixture F3, one Selected per property:** Tick Selected by hand on a second scenario of the Fixture A property. Expected: the property appears in the "more than one Selected" data-quality view. Ask Claude to select a scenario on that property: Claude asks before unticking the other one.

**Fixture G, budget roll-up:** Add Actual amounts to two Rehab lines. Expected: the Project Rehab Actual and Variance roll-ups match the sum.

**Fixture H, CRM alerts:** A company with COI expiration 20 days from today appears in the "COI expiring" view. A paid budget line without a lien waiver appears in the "missing lien waiver" view.

**Fixture I, AI guardrails:** Ask Claude to set Stage to a value that does not exist (for example "Under Contarct"). Expected: Claude refuses or asks, and no new option is created. Ask Claude to delete a test record. Expected: Claude refuses.

## Out of Scope

- Rental operations: unit, lease and tenant databases, and rental and refinance formulas (NOI, cap rate, cash-on-cash, DSCR, BRRRR cash left in deal). Rental fields exist on Property but are hidden.
- Wholesaling and assignment deals.
- The Illinois side of the St. Louis metro (Metro East).
- A separate Draws database.
- Tax-grade bookkeeping. Notion holds budgets and actual costs, but it is not the accounting system.
- Allowing Claude to delete or archive records. This may be enabled later.
- External access to Notion for contractors or agents. Only the 3 team members and the Claude account use the workspace.
- Automatic triggers. Claude runs procedures only when a team member asks.

## Further Notes

- All assumption defaults are confirmed. Loan-to-cost on purchase (70%) may change with the lender; if it does, update the Assumptions record and recompute the Fixture A–C expected values before the acceptance run.
- Procedures start in the AI Operating Manual. After real use, procedures that Claude gets wrong or that are run often (likely Under Contract and Analyze Property) can be promoted to a shared Claude skill whose first step is to read the Manual, so the schema facts stay in one place.
- The interest formula assumes the full loan balance for the full hold period. Real hard money loans often charge interest only on drawn funds, so this formula is conservative. Review it on the first real project.
- In St. Louis City and many St. Louis County municipalities, an occupancy inspection is needed on transfer, and some municipalities hold repair escrow at closing. Always check the specific municipality.
- Notion select fields create a new option when an unknown value is written. The AI Operating Manual's fixed vocabulary is the main protection against this.
- According to secondary sources, the hosted Notion MCP does not expose a delete tool today. This can change, so the "no delete" rule stays in the AI Operating Manual.
- The system has fewer than 10 existing properties, so the migration is small and can be done in one session.
