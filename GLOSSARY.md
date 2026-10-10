# Glossary

Terms used in the REI Notion Operating System (see `REI-Notion-Operating-System-Spec.md`).

## Workspace layout

- **Teamspace**: the single Notion teamspace "REI Operating System" that holds the whole system.
- **Home**: the dashboard page at the top of the teamspace (active projects, deadlines, my tasks, pipeline, COI alerts).
- **Pillar**: one area of the business, with one page in the teamspace. There are four:
  - **Acquisitions**: Properties, Buy Box, Underwriting, Assumptions.
  - **Projects**: Projects, Budget Lines, Bids.
  - **CRM**: Contacts, Companies.
  - **Knowledge**: Knowledge.
  Tasks are not a pillar. They show on Home and on each pillar page as filtered views.
- **Databases page**: the one page that holds all 11 databases. Pillar pages show linked views of them; they never hold a database themselves.
- **Archive – Legacy**: the page that old workspace pages are moved into after migration. Nothing is deleted.
- **Claude account**: the dedicated company-email Notion member (4th seat) that Claude uses through the Notion MCP. No person works in Notion as this account.

## Pipeline

- **Property**: one record per physical house in the Properties database, titled by its full address (for example "1234 Main St, St. Louis, MO 63110"). It is permanent: it stays after the house is bought or sold.
- **Stage**: where a Property is in the pipeline. Exactly one of: Lead, Analyzing, Walkthrough Scheduled, Offer Submitted, Negotiating, Under Contract, Owned, Sold, Passed, Lost.
  - **Pre-contract** stages: Lead, Analyzing, Walkthrough Scheduled, Offer Submitted, Negotiating.
- **Pipeline**: the board view of Properties grouped by Stage.
- **Pass Reason**: why a Property is Passed or Lost. Exactly one of: Price, Rehab Too Heavy, Location, Title, Lost to Other Buyer. Notion cannot make it mandatory; the "Missing Pass Reason" data-quality view catches gaps.
- **Data-quality view**: a filtered view that lists records breaking a rule Notion cannot enforce. It should be empty.
- **Assumptions record**: the one Active record in the Assumptions database that holds the central defaults (buy closing, selling, hard money rate and points, hold months, rehab contingency, loan-to-cost on purchase and rehab). Underwriting scenarios read their defaults from it.
- **Buy Box**: our screening criteria (max price, max scope of work, allowed Niche Grades, min beds/baths/sq ft, min net profit and min profit % of ARV). It is versioned: each **Buy Box version** (for example "v1 – Oct 2026") is one record, and exactly one is **Active**. Each Property links to the version it is judged against.
- **Scenario**: one record in the Underwriting database that analyzes one Property (for example "Flip – conservative ARV"). A Property can have several scenarios.
- **Selected** scenario: the one scenario per Property whose numbers the team acts on. Notion cannot enforce "one"; the "More than one Selected" data-quality view catches extras.
- **Freeze**: when a scenario is Selected, every Effective value is copied into its Override, so later changes to the Assumptions record do not change it. Unticking Selected does not unfreeze it.
- **Base** result: Base Profit from hard numbers only (ARV − Purchase − Buy Closing − Rehab).
- **Fully loaded** result: Net Profit after financing (points, interest), holding, rehab contingency and selling costs.
- **Default / Override / Effective**: for each assumption, a scenario shows the **Default** (read from its Assumptions record), an optional **Override** typed on the scenario, and the **Effective** value the formulas use (the Override if filled in, else the Default).
- **ARV**: after-repair value, the expected sale price after the rehab.
- **MAO**: maximum allowable offer, ARV × 70% − Rehab.
- **LTC**: loan-to-cost, the share of purchase or rehab the hard money lender funds.
- **Below Criteria**: a scenario whose Fully loaded Net Profit is below its Buy Box's Min Net Profit or Min Profit % of ARV.
- **Rental fields**: Market Rent, Actual Rent, Lease End, Tenant Name. They exist on Property but are hidden until rentals start.

## CRM

- **Contact**: one person in the Contacts database. A person with several **Roles** (for example Contractor and Hire Candidate) is still one Contact.
- **Company**: one business in the Companies database (contractor, lender, title company, etc.). Contacts link to it through Company.
- **Hiring Stage**: where a Hire Candidate is in hiring. Exactly one of: Sourced, Screening, Interview, Trial, Hired, Rejected.
- **Company Status**: how far a Company is vetted. Exactly one of: Prospect, Vetted, Preferred, Do Not Use.
- **COI**: certificate of insurance. **COI Expiration** drives the "COI expiring" view, which lists Companies whose COI expires within about 30 days (or already has).
