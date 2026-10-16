# Knowledge database

Built for issue #16. Lives in the Databases page of the "REI Operating System" teamspace.

- Database: https://app.notion.com/p/4309663ce6cc43dbb55d16ce85e8cbbc
- Data source: `collection://64f7ba25-46f0-4e98-9052-29d84db83a53`

## Schema

| Field | Type | Allowed values |
|---|---|---|
| Title | title | Meeting Note "[YYYY-MM-DD] – [topic]"; Glossary Term: the term itself, for example "ARV" |
| Type | select | Meeting Note, Course/Lesson, Glossary Term, SOP, Market Research |
| Summary | text | One or two lines; for a Glossary Term, the definition |
| Date | date | Meeting, lesson or research date |
| Source | URL | One link: a Google Drive file, or a Drive folder for several files (shared with the team), or a web link. Videos: link a transcript |
| Properties | relation → Properties (two-way, synced as Properties › Knowledge) | |
| Projects | relation → Projects (two-way, synced as Projects › Knowledge) | |
| Contacts | relation → Contacts (two-way, synced as Contacts › Knowledge) | |
| Tasks | relation → Tasks (two-way, synced as Tasks › Knowledge) | Action items made from the entry |

The full text (notes, lesson content, SOP steps) goes in the page body, or in a Google Drive file linked in Source.

## Files and asking Claude about them

Files live in Google Drive, not in Notion. An entry's Source links the file (or a folder). Claude answers questions from them with the **Answer from Knowledge** procedure in the AI Operating Manual: it searches Knowledge, opens each Source link with the Google Drive connector, answers from what it read, names the entries and files, and writes nothing. If it cannot open a file, it says so instead of guessing.

For this to work in the team's Claude:

- Both the Notion and the Google Drive connectors are connected for the account the team uses with Claude.
- The Drive files are shared with that Google account (sharing the Knowledge folder once covers everything in it).
- Claude reads Google Docs, Sheets, Slides, PDFs and Word files with real text. It cannot read videos or audio, and scanned images are unreliable: save a transcript next to a video and link that.

## Views

| View | Type | Purpose |
|---|---|---|
| All Knowledge | table | Every entry, grouped by Type, newest Date first |
| 📖 Glossary | table | `Type = Glossary Term`, A–Z, Title and Summary only |
| 📝 Meeting Notes | table | `Type = Meeting Note`, newest first, with the links |
| 🧪 TEST records | table | Title starts with "TEST –", for deleting by hand |

## Glossary entries

ARV, MAO, LTC, EMD and COI, each a Glossary Term with its definition in Summary. The definitions match `GLOSSARY.md`; MAO and LTC also match the Underwriting formulas and Assumptions defaults.

## Test records

| Record | Links |
|---|---|
| TEST – 2026-10-14 – Walkthrough debrief 4521 Fixture Ave (Meeting Note) | Property "TEST – 4521 Fixture Ave", Project "TEST – 4521 Fixture – Flip Rehab – 2026-10 (10 days)", Contact "TEST – Multi-role person", Task "TEST – Order inspection" |
| TEST – 2026-10-16 – Fixture G walkthrough debrief 500 Fixture F (Meeting Note, #18) | Property "TEST – 500 Fixture F St", Project "TEST – 500 Fixture F – Flip Rehab – 2026-10", Contact "TEST – Multi-role person", four Tasks made or linked by Process Meeting Notes |
| TEST – Roof estimating lesson (Drive file) (Course/Lesson) | Source: Google Doc "TEST – Knowledge Drive check (delete me)" in selamoinvest@gmail.com's Drive (https://docs.google.com/document/d/1L2iNceVHOGIx2FIHSvUwd2HXaPIM70qqhhdCNZVQfdc/edit) |

Checked 2026-10-14 through the Notion MCP:

| Check | Result |
|---|---|
| Before the build, no Knowledge database existed (search of the Databases page) | Red |
| Fetching the property, the project, the task and the contact: each one's Knowledge field lists the meeting note | Pass |
| The 📖 Glossary view, queried in view mode, returns exactly ARV, COI, EMD, LTC, MAO | Pass |
| The 🧪 TEST records view returns only the meeting note | Pass |
| Answer from Knowledge, run by a fresh agent that got only the question and the instruction to read the Manual: "how much should I budget per square foot for a roof replacement, what do I add if the decking is bad, and what's the code word for that lesson?" The answers exist only in the Google Doc, not in Notion | Pass. It answered $9.75 per sq ft, $1,400 for decking over 20%, "BLUE HERON 47"; named the entry and the Doc; flagged that both are TEST records; made no writes |

The glossary terms are real entries, not test records. Delete the TEST entries and the TEST Google Doc by hand when no longer needed.

## AI Operating Manual

Updated in the same issue: Knowledge in the database map (section 2), a Knowledge field dictionary with a pointer to the 📖 Glossary view (section 3), Knowledge relation rows on Properties and Contacts, and a Knowledge naming rule (section 5). The follow-up added the Answer from Knowledge procedure (section 6) and the Drive guidance on Source.

## Processing meeting notes

Asking Claude to "process meeting notes" runs the **Process Meeting Notes** procedure in the AI Operating Manual (#18): each action item in the note becomes a verb-first Task, linked to the note's Property and Project, and the note's Tasks field lists them. Existing open tasks that cover an item are linked instead of duplicated, and Claude writes only listed Priority values. Fixture G checked it (see the Manual's fixtures).

## Not built yet

- A Knowledge linked view on the Knowledge pillar page.
