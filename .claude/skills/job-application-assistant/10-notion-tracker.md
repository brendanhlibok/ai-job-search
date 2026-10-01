---
framework_version: 1.0.0
---

# Notion Job-Application Tracker

The single source of truth for the Notion schema, the one-time setup, and the upsert logic that `/appliedto` uses. There is no separate setup command — `/appliedto` runs the One-Time Setup below itself, the first time it's needed, the same way `05-cv-templates.md`'s Google Doc setup runs lazily inside `generateresume` on first use. Documented here, once, so that logic has a single home.

<!-- BEGIN ACTIVE-NOTION-DATABASES (set once during one-time setup - do not edit by hand) -->
> **Parent page:** [Job Search Tracker](https://app.notion.com/p/3ecf675e5c5a81f8b03dcbbb77846f06) (under the user's existing "Career" page) (ID: `3ecf675e-5c5a-81f8-b03d-cbbb77846f06`)
> **Job Applications database:** [link](https://app.notion.com/p/603106b22afa4703a0a4830bcc7e94bf) (data source ID: `229edeaf-60ff-4b91-bbba-00f8cd82626c`)
> **Contacts database:** [link](https://app.notion.com/p/9830de40025143c18e65b953a8615169) (data source ID: `b6e1645b-82e1-400a-93fc-36c9df2df031`)
> **Interview Stages database:** [link](https://app.notion.com/p/d0dd7bbd6ece4b9ebcb386fc6ae693f7) (data source ID: `a90249c6-c771-4a36-9c0f-24990678abde`)
> **Set up:** 2026-09-30
<!-- END ACTIVE-NOTION-DATABASES -->

Job Applications is set to display **inline** on the Job Search Tracker page (visible immediately, not a sub-page click-through) — Contacts and Interview Stages are left as regular full-page databases, reachable from Job Applications' relation columns.

## Design

**Notion is the live system of record** for application tracking — not a mirror of anything in this repo. `job_search_tracker.csv` is a retired historical artifact with zero read/write dependency from this system. `generateresume` never writes here — it only produces the resume. The user triggers `/appliedto` themselves, by dropping a link, once they've actually submitted an application, and that's the only thing that creates a row. Everything else about an application's progress (contacts, interview rounds) is entered by the user directly in Notion.

## One-Time Setup

Run once, automatically, whenever `/appliedto` finds the `ACTIVE-NOTION-DATABASES` block above still unconfigured — not a separate command.

1. Search the Notion workspace for a page titled "Careers" (case-insensitive, top-level or nested). If none is found, or more than one matches, list what was found and ask the user rather than guessing.
2. Create a page titled "Job Search Tracker" under it.
3. Create the three databases **in dependency order** (a relation property must target an already-existing database):
   1. **Job Applications** first — no relations needed yet.
   2. **Contacts**, with its `Job Application` relation property targeting the Job Applications database.
   3. **Interview Stages**, the same way.
4. Write the `ACTIVE-NOTION-DATABASES` block with all three database IDs/URLs, the parent page ID/URL, and today's date.
5. Continue with the upsert that triggered setup — don't make the user re-run anything.

## Database schema

### Job Applications (parent database)

| Property | Type | Notes |
|---|---|---|
| Name | title | `<Job title> — <Company>` (Notion's mandatory title field) |
| Company | rich text | always-visible field |
| Job title | rich text | always-visible field |
| Location | rich text | always-visible field |
| Link to the job posting | url | always-visible field |
| Date applied | date | always-visible field; set to today at creation by `/appliedto` (the command only runs once the user has actually applied), or to a date the user gives it explicitly |
| Status | select | `applied` / `interview` / `offer` / `hired` / `rejected` / `no_response` / `offer_declined` / `withdrawn` — background property. No `drafted` state in this design: a row only exists once `/appliedto` has recorded a real submission |
| Application Method | select | exactly: `cold apply`, `referral`, `recruiter reached out to me`, `I reached out to recruiter`, `cold apply with LinkedIn InMail`, `cold apply with email message`, `cold apply with connection note` — never set by `/appliedto`; the user sets it directly in Notion |
| Experience Alignment | number (1-5) | see growth note below |
| Excitement | number (1-5) | how excited the user is about this specific role |
| Contacts | relation (auto, reciprocal) | created automatically once Contacts' own relation property targets this database — shows linked contacts as chips directly on the row |
| Interview Stages | relation (auto, reciprocal) | same, from Interview Stages |
| Key | rich text | dedup anchor: `<company>_<job-title>`, lowercased, non-alphanumeric collapsed to a single underscore — never hand-edited |

No Resume Doc / Resume URL property — deliberately excluded per explicit instruction.

**Growth note (Experience Alignment):** ships as one Number property. When sub-fields (YOE, technical skills, soft skills) are wanted later, the clean migration is promoting it to its own linked database (same shape as Contacts/Interview Stages below) rather than retrofitting multiple properties onto Job Applications — non-breaking, no data loss either way. Not building this now.

### Contacts (child database, relation → Job Applications)

| Property | Type |
|---|---|
| Name | title |
| Email | email |
| LinkedIn | url |
| Phone | phone_number |
| How I Know Them | rich text |
| Position | rich text |
| Job Application | relation → Job Applications |

### Interview Stages (child database, relation → Job Applications)

| Property | Type | Notes |
|---|---|---|
| Stage | title | free text, e.g. "Phone Screen — 2026-10-05" |
| Stage Type | select | `phone_screen` / `technical` / `case` / `onsite` / `final_round` — matches `07-interview-prep.md`'s stage vocabulary |
| Date | date | |
| Notes/Outcome | rich text | |
| Job Application | relation → Job Applications | |

Both relations surface automatically on the Job Applications board via the reciprocal property Notion creates — contacts and interview rounds are visible and clickable from the main table without opening either child database. A Rollup property (e.g. "furthest stage reached") can summarize linked data inline later; not built day-1.

## Dedup key

`Key = <company>_<job-title>`, lowercased, with every run of non-alphanumeric characters collapsed to a single underscore. Used to find an existing row before creating a new one.

## Upsert routine

Called by `/appliedto`.

1. Compute `Key` from the company and job title given.
2. Query the Job Applications database for a page whose `Key` property equals it.
3. **No match** → create a page with exactly: Company, Job title, Location, Link, Key, Status set to `applied`, and Date applied set to today (or a date the user gave explicitly). That's the full set `/appliedto` ever writes. Leave Application Method, Experience Alignment, and Excitement empty — not this command's job; the user sets them directly in Notion.
4. **Match** (the same role was already recorded — e.g. the user runs `/appliedto` twice for it) → do not create a duplicate. Tell the user a row already exists and link it. Only change Status if the new call reports a further-along stage, and never downgrade one (`interview`, `offer`, `hired`, etc.) back toward `applied`. Company, Job title, Location, Link, and Key are immutable after creation — they're the dedup anchor's own inputs.
5. **Never create or touch rows in Contacts or Interview Stages** — see Important Rules.

## Status vocabulary

`applied`, `interview`, `offer`, `hired`, `rejected`, `no_response`, `offer_declined`, `withdrawn`

Always write these exact lowercase-underscore spellings. Never write a space-separated variant.

## Important Rules

1. **Contacts and Interview Stages are purely user-maintained.** No command — not `/appliedto`, not `/generateresume` (which never touches Notion at all), not `/interview`, not `/outcome` — ever writes a row into either database. They exist so the user can add rows by hand in Notion and link them to the right Job Applications row themselves via the relation property. This is a deliberate scope boundary, not an oversight — don't wire automatic writes into them later without a conscious decision to change this contract.
2. **Idempotent upsert on `Key`.** Re-running for the same company + job title never creates a duplicate row.
3. **Never delete or archive a Job Applications page.**
4. **Status normalization before every write.** Map to the canonical vocabulary above; never push a value outside that set.
5. **Setup runs at most once.** If the managed block is already filled in, never re-run One-Time Setup or touch an existing database's properties — there is currently no repair path; that's a deliberate simplification until a real need for one shows up.
