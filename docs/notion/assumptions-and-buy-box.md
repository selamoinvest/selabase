# Assumptions and Buy Box databases

Built for issue #5. Both live in the Databases page of the "REI Operating System" teamspace.

- Assumptions: https://app.notion.com/p/d5cab58dbbb045f3bcd2e702904d2db4 — data source `collection://2b132355-b1a8-49b8-9629-50b3dde82cc0`
- Buy Box: https://app.notion.com/p/97933f34ae6d4bd69845f831823d2196 — data source `collection://5d6cbfda-db05-4e99-b96f-a4d67706110b`

## Assumptions schema

| Field | Type | Notes |
|---|---|---|
| Name | title | |
| Active | checkbox | Only one record is Active |
| Buy Closing % | number (%) | % of purchase price |
| Selling % | number (%) | % of ARV |
| Hard Money Rate | number (%) | Annual, interest-only |
| Points % | number (%) | % of loan amount |
| Hold Months | number | |
| Rehab Contingency % | number (%) | % of rehab |
| LTC Purchase % | number (%) | Loan-to-cost on purchase |
| LTC Rehab % | number (%) | Loan-to-cost on rehab |

Percent fields store fractions (3% is stored as 0.03), which is what the Underwriting formulas multiply by.

### Central record

"Defaults" (https://app.notion.com/p/3f5226043a8b810d94b8f6f2457a30ae), Active:

| Field | Value |
|---|---|
| Buy Closing % | 3% |
| Selling % | 8% |
| Hard Money Rate | 12% |
| Points % | 2% |
| Hold Months | 6 |
| Rehab Contingency % | 0% |
| LTC Purchase % | 70% |
| LTC Rehab % | 100% |

## Buy Box schema

| Field | Type | Notes |
|---|---|---|
| Version Name | title | For example "v1 – Oct 2026" |
| Active | checkbox | Only one version is Active |
| Max Purchase Price | number ($) | |
| Max Scope of Work | number ($) | |
| Allowed Niche Grades | multi-select | Same 13 grades as Property Niche Grade (A+ to F) |
| Min Beds, Min Baths, Min Sq Ft | number | |
| Min Net Profit | number ($) | Fully loaded Net Profit |
| Min Profit % of ARV | number (%) | Fully loaded Net Profit as % of ARV |
| Properties | relation (synced from Properties › Buy Box) | |

### v1

"v1 – Oct 2026" (https://app.notion.com/p/3f5226043a8b813988a1e85392d2efba), Active: Max Purchase Price $250,000, Max Scope of Work $40,000, Allowed Niche Grades C+ / B- / B, Min Beds 3, Min Baths 1, Min Sq Ft 800, Min Net Profit $30,000, Min Profit % of ARV 15%.

## Properties › Buy Box

Properties has a **Buy Box** relation (two-way, synced as Buy Box › Properties), shown in the Deal section of the record page and in the All Properties view. Notion cannot limit it to one version; the Update Buy Box procedure keeps it to one.

## Views

| Where | View | Purpose |
|---|---|---|
| Assumptions | All Assumptions | Every record |
| Buy Box | All Versions | Every version, with its linked Properties |
| Acquisitions page | Active Buy Box | Linked view, Active = checked |
| Acquisitions page | Active Assumptions | Linked view, Active = checked |

## Not built yet

- Underwriting relation to Assumptions, and the database templates that prefill the Active Assumptions record and Active Buy Box (issue for Underwriting).
- Buy Box Fit formula on Properties (needs Underwriting for the SOW and profit criteria).
- Notion cannot enforce "only one Active". There is no data-quality view for it yet; the Active views above show more than one row if it happens.

## Test records

"TEST – Buy Box relation check, St. Louis, MO 63110" in Properties (Stage Lead, linked to v1) proves the relation reads back on both sides. Delete by hand when no longer needed.
