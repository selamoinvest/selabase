# Contacts and Companies databases

Built for issue #4. Both live in the Databases page of the "REI Operating System" teamspace.

- Contacts: https://app.notion.com/p/30781b932325446dab06fcad98eef15a — data source `collection://33b353e9-59ea-408b-ab20-a48ebe71c041`
- Companies: https://app.notion.com/p/b7a984f4cca742fe88e90e34060c16fc — data source `collection://72d7de70-bf91-41f3-8680-8c03d38a9f43`

## Contacts schema

| Field | Type | Allowed values |
|---|---|---|
| Name | title | Person's name |
| Roles | multi-select | Contractor, Agent, Lender, Title, Inspector, Appraiser, Insurance, Attorney, Property Manager, Wholesaler, Hire Candidate |
| Phone | phone | |
| Email | email | |
| Company | relation → Companies (two-way, synced as Companies › Contacts) | |
| Source / Referred By | text | |
| Hiring Stage | select | Sourced, Screening, Interview, Trial, Hired, Rejected |

One person with several roles is one Contact record with several Roles selected.

## Companies schema

| Field | Type | Allowed values |
|---|---|---|
| Name | title | |
| Trades | multi-select | General, Demo, Framing, Roofing, Plumbing, Electrical, HVAC, Drywall, Flooring, Painting, Carpentry, Concrete, Landscaping |
| License # | text | |
| COI Expiration | date | |
| W-9 on File | checkbox | |
| Status | select | Prospect, Vetted, Preferred, Do Not Use |
| Rating | select | 1–5 |
| Contacts | relation (synced from Contacts › Company) | |

The spec does not list Trades or Rating values; the Trades list and the 1–5 Rating scale are starting choices and can be edited.

## Views

| Database | View | Purpose |
|---|---|---|
| Companies | ⚠️ COI expiring (30 days) | COI Expiration is set and on or before one month from today, sorted soonest first. Already-expired COIs also show, which is intended. |

The Notion view DSL only offers "one month from now" as a relative date, so "30 days" is approximated as one month.

## Not built yet

- Relations from Bids, Budget Lines, Projects (Lender), Tasks (External Party) and Knowledge, added with those databases.
- The "paid vendors with no W-9" view (needs Budget Lines).

## Test records

Records titled "TEST –" in both databases (Fixture H, part 1: a company with COI 20 days out, a company 90 days out that must not appear, and a Contact with two roles). Delete by hand when no longer needed.
