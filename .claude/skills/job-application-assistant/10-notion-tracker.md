---
framework_version: 1.0.0
---

# Notion Job-Application Tracker

The single source of truth for the Notion schema, the one-time setup, and the create logic that `/appliedto` uses. There is no separate setup command — `/appliedto` runs the One-Time Setup below itself, the first time it's needed, the same way `05-cv-templates.md`'s Google Doc setup runs lazily inside `generateresume` on first use. Documented here, once, so that logic has a single home.

<!-- BEGIN ACTIVE-NOTION-DATABASES (set once during one-time setup - do not edit by hand) -->
> **Parent page:** [Job Search](https://app.notion.com/p/3ecf675e5c5a81f8b03dcbbb77846f06) (under the user's existing "Career" page) (ID: `3ecf675e-5c5a-81f8-b03d-cbbb77846f06`)
> **Job Application database:** [DB Applications](https://app.notion.com/p/76ff675e5c5a834e995c01a0e79e4c6c) (data source ID: `596f675e-5c5a-83d3-b7a4-8761407a86ed`), nested inside the [Job Application Tracker](https://app.notion.com/p/b9df675e5c5a82339b750164b4a2348d) page — a Notion template the user installed directly, adopted as the system of record 2026-10-01
> **Contacts database:** [link](https://app.notion.com/p/9830de40025143c18e65b953a8615169) (data source ID: `b6e1645b-82e1-400a-93fc-36c9df2df031`)
> **Interview Stages database:** [link](https://app.notion.com/p/d0dd7bbd6ece4b9ebcb386fc6ae693f7) (data source ID: `a90249c6-c771-4a36-9c0f-24990678abde`)
> **Set up:** 2026-09-30 (original "Job Application" database, trashed 2026-10-01). **Replaced 2026-10-01** by "DB Applications" above — see Visible/Hidden split below
<!-- END ACTIVE-NOTION-DATABASES -->

DB Applications is nested inside the Job Application Tracker page (the user's own template) under Job Search — Contacts and Interview Stages are left as regular full-page databases, reachable from DB Applications' relation columns. The previous "Job Application" database (inline on Job Search directly) was trashed 2026-10-01 once its 2 real rows were migrated here — see Migration history below.

## Visible / Hidden split

The Default view on DB Applications only displays: **Company (title), Position, Location, Status, Date applied, Link, Next Action.** Everything else — Application Method, Experience Alignment, Excitement Level, Contacts, Interview Stages — still exists as real properties on the same row, just hidden from that view (Notion's per-view "hide property" toggle, not a separate table). The user reveals a hidden property from a row by opening it, or unhides it workspace-wide from the view's property panel. No data lives outside Notion — this is a single system of record, consistent with the Design section above.

## Migration history (2026-10-01)

The user installed a Notion template page ("Job Application Tracker", containing a "DB Applications" database) independently of this system. Rather than maintain two parallel trackers, it replaced the original "Job Application" database as the system of record:
- **Schema changes to DB Applications:** dropped `Salary`, `Website`, and `Contact` (plain email text — superseded by the `Contacts` relation below); renamed `Reference Link` → `Link` and `Application Date` → `Date applied`; converted `Status` from multi-select to single-select with this system's canonical vocabulary (see Status vocabulary below); added `Location`, `Application Method`, `Experience Alignment`, `Excitement Level`. `Next Action` (multi-select: Follow up / Waiting / Prepare Interview / Send email / Decide) is kept from the template, visible, as a useful to-do flag — it isn't part of this system's original spec and `/appliedto` never writes to it.
- **Contacts and Interview Stages** had their `Job Application` relation property retargeted from the old database to DB Applications (same two child databases, same IDs — only the relation target changed).
- **2 real rows** (Niantic Spatial, Orthofix) were migrated by hand into DB Applications with the new schema; the Interview Stages row linked to Niantic Spatial was re-pointed at the migrated row.
- **8 demo rows** that shipped with the template (Apple, Amazon, Tesla, Spotify, Meta, Netflix, Airbnb, Adobe — fictional placeholder applications) were left in place: the available Notion tools can edit schema and properties but cannot delete/archive pages, so removing them requires the user to select and delete them manually in Notion.
- The old "Job Application" database (and its 2 blank test rows) was moved to Notion's trash, not permanently deleted — recoverable from Notion's UI if needed.

## Bulk import history (2026-10-02)

187 historical applications were bulk-imported from the user's "Job Applications" Google Sheet (Drive, not this repo) into DB Applications, via `notion-create-pages` in 5 batches of up to 40 rows (the tool's per-call limit is 100; 40 was chosen for reliability). Only `Company` (as the title), `Position` (from the sheet's `Role` column), `Location`, and `Link` were copied; `Status` was set to `applied` for every row per explicit instruction ("assume yes for all of them") regardless of what the sheet's own `Applied` column said; `Next Action` was left empty. One sheet row (Mammoth Brands) was skipped as a duplicate — it was already in DB Applications from a prior single-row `/appliedto` run. Two sheet rows with no Company (stray contact notes for Jamie Parmele and Stefan Sens, not real applications) were also skipped. Several imported rows have blank `Position`/`Location`/`Link` because the source sheet itself was blank there — not fabricated. **Contacts and Interview Stages were deliberately untouched by this import** — see Important Rules below.

## Design

**Notion is the live system of record** for application tracking — not a mirror of anything in this repo. `job_search_tracker.csv` is a retired historical artifact with zero read/write dependency from this system. `generateresume` never writes here — it only produces the resume. The user triggers `/appliedto` themselves, by dropping a link, once they've actually submitted an application, and that's the only thing that creates a row. Everything else about an application's progress (contacts, interview rounds) is entered by the user directly in Notion.

## One-Time Setup

Run once, automatically, whenever `/appliedto` finds the `ACTIVE-NOTION-DATABASES` block above still unconfigured — not a separate command.

1. Search the Notion workspace for a page titled "Careers" (case-insensitive, top-level or nested). If none is found, or more than one matches, list what was found and ask the user rather than guessing.
2. Create a page titled "Job Search Tracker" under it.
3. Create the three databases **in dependency order** (a relation property must target an already-existing database):
   1. **Job Application** first — no relations needed yet.
   2. **Contacts**, with its `Job Application` relation property targeting the Job Application database.
   3. **Interview Stages**, the same way.
4. Write the `ACTIVE-NOTION-DATABASES` block with all three database IDs/URLs, the parent page ID/URL, and today's date.
5. Continue with the create that triggered setup — don't make the user re-run anything.

## Database schema

### DB Applications (parent database)

| Property | Type | Notes | Visible in Default view? |
|---|---|---|---|
| Company | title | Notion's mandatory title field — this database uses Company itself as the title, unlike the pre-2026-10-01 design which had a separate `Name` field | yes |
| Position | rich text | | yes |
| Location | rich text | added 2026-10-01 — the template didn't have it | yes |
| Link | url | (named "Reference Link" in the original template) | yes |
| Date applied | date | set to today at creation by `/appliedto` (the command only runs once the user has actually applied), or to a date the user gives it explicitly (named "Application Date" in the original template) | yes |
| Status | **multi_select** (intended as single-select) | `applied` / `interview` / `offer` / `hired` / `rejected` / `no_response` / `offer_declined` / `withdrawn` — background property. No `drafted` state in this design: a row only exists once `/appliedto` has recorded a real submission. **Known bug:** the 2026-10-01 `ALTER COLUMN ... SET SELECT(...)` call that was meant to convert this from the template's multi-select did not actually take — Notion still stores it (and returns it via the API) as a JSON array, e.g. `["interview"]`. Functionally harmless so far (every row has exactly one value), but technically permits more than one. Not fixed yet — flagged to the user 2026-10-01, fix deferred until they ask for it | yes |
| Next Action | multi-select | `Follow up` / `Waiting` / `Prepare Interview` / `Send email` / `Decide` — kept from the original template as a to-do flag; `/appliedto` never writes to it | yes |
| Application Method | select | exactly: `cold apply`, `referral`, `recruiter reached out to me`, `I reached out to recruiter`, `cold apply with LinkedIn InMail`, `cold apply with email message`, `cold apply with connection note` — never set by `/appliedto`; the user sets it directly in Notion | hidden |
| Experience Alignment | number (1-5) | see growth note below | hidden |
| Excitement Level | number (1-5) | how excited the user is about this specific role | hidden |
| Contacts | relation (auto, reciprocal) | created automatically once Contacts' own relation property targets this database — shows linked contacts as chips on the row when unhidden; see Page layout below for how it's surfaced on the individual page | hidden |
| Interview Stages | relation (auto, reciprocal) | same, from Interview Stages | hidden |

No Resume Doc / Resume URL property, no Salary, no Website, no plain-text Contact field, no Key identifier, no Contact Backgrounds rollup — deliberately excluded or removed; see Migration history above.

**Analytics note (removed 2026-10-01):** a `Contact Backgrounds` rollup (`ROLLUP('Contacts', 'How I Know Them', 'show_original')`) briefly existed here to answer "which jobs I interviewed for had a contact from Rice" — the `Contacts` relation alone can't be filtered by a property that lives on the *related* contact, so the rollup pulled `How I Know Them` tags onto the application row where they'd be filterable. The user asked to remove it from the page shortly after it was added; dropped outright rather than just hidden. `Contacts.How I Know Them` (multi-select, including a `Rice Alumnus/Alumna` option) is still there, so the same rollup can be re-added later in one `ADD COLUMN ... ROLLUP(...)` call if this analytics need comes back.

**Growth note (Experience Alignment):** ships as one Number property. When sub-fields (YOE, technical skills, soft skills) are wanted later, the clean migration is promoting it to its own linked database (same shape as Contacts/Interview Stages below) rather than retrofitting multiple properties onto DB Applications — non-breaking, no data loss either way. Not building this now.

### Contacts (child database, relation → DB Applications)

| Property | Type | Notes |
|---|---|---|
| Name | title | |
| Email | email | |
| LinkedIn | url | |
| Phone | phone_number | |
| How I Know Them | **multi-select** (was a single-option select; documented here as "rich text" before 2026-10-01, which was already stale) | Options as of 2026-10-01: `Linkedin Search`, `Rice Alumnus/Alumna`, `Former Coworker`, `Mutual Connection`, `Referral/Introduction`, `Recruiter Outreach`, `Cold Outreach`, `Other`. Converting to multi-select (and adding real options beyond the one-off "Linkedin Search") is what makes `Contact Backgrounds` on DB Applications possible — see Analytics note above. Add more options directly in Notion as new kinds of connections come up; no code depends on this exact list |
| Position | rich text | |
| Job Application | relation → DB Applications | |

### Interview Stages (child database, relation → DB Applications)

**Redesigned 2026-10-01: one row per application, not one row per round.** The row's title is the company name (matching the linked DB Applications row), and each interview round is a plain text column on that same row — e.g. `Round 1` = "30 min HR interview". This replaced the original per-round design (`Stage`/`Stage Type`/`Date`/`Notes/Outcome`, one row per interview round) because the user wanted rounds laid out as columns across a single application row, not as separate rows. Notion's title property can't be a formula/rollup that auto-mirrors the linked DB Applications row's title — it's always plain text — so the title has to be set explicitly to the company name when the row is created (it won't auto-update if the company name changes on the DB Applications side later).

| Property | Type | Notes |
|---|---|---|
| Company | title | plain text, set explicitly to match the linked DB Applications row's `Company` — not a live mirror (named `Stage` before the 2026-10-01 redesign) |
| Round 1 ... Round 6 | rich text | free text per round, e.g. "30 min HR interview" — 6 provisioned since the user's old spreadsheet tracker went up to 5 interview rounds plus an offer stage; add more with `ADD COLUMN "Round 7" RICH_TEXT` etc. if an application ever needs more |
| Job Application | relation → DB Applications | |

No `Stage Type` select or `Notes/Outcome` field anymore — the user asked to remove both in the 2026-10-01 redesign so "the rest of the columns [are] just text columns that include round 1, round 2, etc."; fold any type/outcome detail directly into the relevant Round's text instead of a separate field. No `Date` field either — it was in this doc's original design but was **never actually present** in the live database even before the redesign (a pre-existing doc/reality mismatch, not something changed by the redesign).

Both relations surface automatically on the DB Applications board via the reciprocal property Notion creates — contacts and interview rounds are visible and clickable from the main table without opening either child database.

## Page layout (DB Applications, added 2026-10-01)

The core mental model: **a DB Applications row is a position**, carrying the always-visible fields (Company/Position/Location/Status/Date applied/Link/Next Action). **That row's page is where the hidden info lives** (Application Method, Experience Alignment, Excitement Level, Contacts, Interview Stages) — hidden from the Default table view, but not buried once you open the row. `Contacts` and `Interview Stages` specifically are relations to their own linked child databases (not plain values) because each contact or interview round carries multiple detailed fields of its own (a contact has email/LinkedIn/position/how-you-know-them; an interview round has a date, stage type, and notes) — too much structure to flatten into one property on the application row.

Each DB Applications page has a custom `page_layout` (set via `notion-update-data-source`'s `page_layout` param):
- **`title` (plain, no `pinnedProperties`) followed by a `properties` block in the main body.** This renders the familiar vertical stacked list — every property on its own row, label left / value right — rather than the horizontal row of compact pills that `title.pinnedProperties` produces. Two earlier iterations used `pinnedProperties` (first narrowly, then for every property) and both read as "the hidden categories got deleted" or "wrong layout," even though the schema was never touched — the plain `properties` main-body block is what actually matches the look the user wants ("vertical like before"). An explicit per-property list (individual `{"type":"property","property":"<name>"}` modules, tried in order to selectively exclude one property from the list) was rejected by the API — likely because relation-type properties (`Contacts`, `Interview Stages`) can't be used in that single-property module shape. The blanket `properties` module is the one that actually works, and it always shows every property that exists — the only way to remove one from the page is to remove the property itself (see Contact Backgrounds in the Analytics note above).
- **An embedded `views` block for the `Contacts` relation**, as its own block in the page body below the properties list. Because `Contacts` is a two-way relation, this renders as a mini linked-table of that application's contacts right there, with Notion's native "+ New" row — clicking it creates a new Contacts page already linked back to this application via `Job Application`. This is the mechanism for "add a contact from the application's page."
- No `sidebar` module — everything lives in `main` now (cover, title, properties, editor, the Contacts view, discussions).

If this layout ever needs to change again, re-fetch the data source first to see the current `page_layout` before writing a new one — the call replaces the whole layout, not just the parts named.

## Key field (removed 2026-10-01)

The original "Job Application" database used to carry a `Key = <company>_<job-title>` rich-text identifier, lowercased with non-alphanumeric runs collapsed to a single underscore, written on every new row. It was **never used to look up or prevent duplicates** — removed outright during the 2026-10-01 visible/hidden schema rework since it wasn't part of the user's spec and had no other function. DB Applications (the current database, adopted later the same day) never had this property. Create routine and `/appliedto` don't reference it.

## Create routine

Called by `/appliedto`.

1. Create a page with exactly: Company, Position, Location, Link, Status set to `applied`, and Date applied set to today (or a date the user gave explicitly). That's the full set `/appliedto` ever writes. Leave Application Method, Experience Alignment, and Excitement Level empty — not this command's job; the user sets them directly in Notion.
2. **Never create or touch rows in Contacts or Interview Stages** — see Important Rules.
3. **No duplicate check.** Re-running `/appliedto` for the same company + job title creates a second row. If the user did this by mistake, they delete the extra row themselves in Notion.

## Status vocabulary

`applied`, `interview`, `offer`, `hired`, `rejected`, `no_response`, `offer_declined`, `withdrawn`

Always write these exact lowercase-underscore spellings. Never write a space-separated variant.

## Important Rules

1. **Contacts and Interview Stages are purely user-maintained.** No command — not `/appliedto`, not `/generateresume` (which never touches Notion at all), not `/interview`, not `/outcome`, not a bulk import — ever writes a row into either database, or sets either relation property on a DB Applications row. They exist so the user can add rows by hand in Notion and link them to the right DB Applications row themselves via the relation property. This is a deliberate scope boundary, not an oversight — the user explicitly restated it 2026-10-02 ("You won't add contacts or interview information unless I say so, even with the appliedto command") after a bulk import (see Migration history) touched only Company/Position/Location/Link/Status/Next Action — don't wire automatic writes into Contacts or Interview Stages later without the user explicitly asking first.
2. **No dedup.** Re-running for the same company + job title creates a second row — this is expected, not a bug.
3. **Never delete or archive a DB Applications page.**
4. **Status normalization before every write.** Map to the canonical vocabulary above; never push a value outside that set.
5. **Setup runs at most once.** If the managed block is already filled in, never re-run One-Time Setup or touch an existing database's properties — there is currently no repair path; that's a deliberate simplification until a real need for one shows up.
