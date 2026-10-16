# Tasks database

Built for issue #13. Lives in the Databases page of the "REI Operating System" teamspace.

- Database: https://app.notion.com/p/eb4ec8395a44485a9d0003c11575af09
- Data source: `collection://c2429a0f-3210-4b0e-98bf-bf7439b7e1d5`

## Schema

| Field | Type | Allowed values |
|---|---|---|
| Task | title | Starts with a verb, for example "Order inspection" |
| Status | status | Not Started (To-do); In Progress, Waiting on External (In progress); Done, Cancelled (Complete) |
| Priority | select | High, Medium, Low |
| Due Date | date | |
| Owner | person | Notion member |
| External Party | relation → Contacts (two-way, synced as Contacts › Tasks) | The contractor, agent etc. we wait on |
| Property | relation → Properties (two-way, synced as Properties › Tasks) | |
| Project | relation → Projects (two-way, synced as Projects › Tasks) | |
| Knowledge | relation → Knowledge (synced side of Knowledge › Tasks, #16) | The note or SOP the task came from. Set from the Knowledge side |

Status is a Notion status field, not a select, so Done and Cancelled count as complete in Notion's own task features.

## Views

| View | Type | Purpose |
|---|---|---|
| All Tasks | table | Every task, sorted by Due Date |
| ✅ My Tasks | table | `Owner = me`, Status not Done or Cancelled, sorted by Due Date |

"Me" is the person looking at the view, so one "My Tasks" view serves every team member.

## Test records

Fixture for issue #13, all linked to "TEST – 4521 Fixture Ave" and "TEST – 4521 Fixture – Flip Rehab – 2026-10 (10 days)" (checked 2026-10-14 through the Claude account, which is "me" when the MCP queries the view):

| Task | Owner | Status | In My Tasks (Claude account) |
|---|---|---|---|
| TEST – Order inspection | Claude account | Waiting on External, External Party "TEST – Multi-role person" | Yes |
| TEST – Get insurance quote | Other team member | Not Started | No |
| TEST – Confirm title commitment (done) | Claude account | Done | No |

The Property's Tasks field and the Project's Tasks field each list all three. Delete by hand when no longer needed.

## Not built yet

- **Recurring tasks** (manual): the Notion MCP cannot create database templates or set their repeat schedule. A team member makes each recurring task a template in the Notion UI (New ▾ → New template) and sets "Repeat" on it. Claude does not recreate recurring tasks.
- Tasks linked views on Home and each pillar page.
