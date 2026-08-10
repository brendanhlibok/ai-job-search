---
framework_version: 2.0.0
---

# Outreach Message Templates and Tailoring Guide

<!-- BEGIN ACTIVE-GOOGLE-DOC-TEMPLATE (set once during one-time setup - do not edit by hand) -->
> **Style examples folder:** *not yet configured* — see "One-Time Setup" below.
<!-- END ACTIVE-GOOGLE-DOC-TEMPLATE -->

## Concept: One "Outreach Message," Three Possible Shapes

"Cover letter" used to mean one fixed document. It's now the broader concept of **outreach**: whatever piece of writing accompanies the CV to introduce you and make the case for an interview. For a given job, the right shape is one of three:

1. **LinkedIn message** — a short note (hard 200-character limit) to a warm connection at the company.
2. **Email** — a direct message to a recruiter, hiring manager, or team inbox.
3. **Cover letter** — the traditional full document, drafted into a Google Doc, for postings that expect one.

Only the cover-letter format produces a Google Doc. The other two are text, delivered where they're sent (a LinkedIn message box, an email body).

## One-Time Setup

Ask for a Google Drive folder ("Outreach Examples") containing past LinkedIn messages, emails, and cover letters actually sent — real examples, not aspirational ones. Confirm access (Drive MCP `list`/`search`) and record the folder ID in the managed block above. These examples are read for **tone, structure, and typical length actually used** — salutation style, how an opening line lands, how a closing reads — never duplicated verbatim into a new draft. This mirrors how `03-writing-style.md` already treats writing-style extraction: a style reference, not a fact source, and never a phrasing to copy wholesale into an application for a different company.

If the folder is empty or a given format has no examples yet, fall back to the format specs below on their own — they're written to stand alone.

## Step 1: Decide the Format (propose, then confirm)

Before drafting anything, assess the signals available for this application and **propose one format with your reasoning**, then use `AskUserQuestion` to confirm or let the user override before drafting. Never silently pick a format.

| Signal | Points toward |
|---|---|
| A named contact who is a 1st-degree LinkedIn connection (former colleague, alum, referral) | **LinkedIn message** |
| A direct recruiter/hiring-manager email address is known or discoverable | **Email** |
| The application portal has an explicit cover-letter upload field, or the posting asks for one | **Cover letter** |
| Small company/startup, informal tone elsewhere in the posting, no cover-letter field | **Email** (or LinkedIn message, if a connection exists) leaning over a formal cover letter |
| Large/formal organization, structured portal, no personal contact identified | **Cover letter** |
| No contact, no cover-letter field, portal-only submission | Default to **cover letter** unless the user says otherwise — it's the safest fallback when there's no signal either way |

A LinkedIn message and a cover letter are not mutually exclusive with each other in principle (a warm-connection note plus a formal application through the portal), but default to drafting **one** unless the user asks for both — most applications only need one entry point.

## Format 1: LinkedIn Message

**Hard 200-character limit.** This is not a soft target — count characters, not words, before presenting the draft. It matches LinkedIn's connection-note length, since this format is for reaching an existing 1st-degree connection, not a cold InMail to a stranger.

Structure: one sentence of context (who you are / the specific role) + one clear, low-friction ask (a quick chat, a referral, a pointer to who to talk to). No pitch, no bullet list, no attachment mention — there's no room, and the CV isn't attached to a LinkedIn message anyway; if relevant, offer to send it in a follow-up reply.

**Draft example shape** (not a template to fill blindly — write to the actual relationship):
> Hi [Name] — saw [Company] is hiring for [Role] and thought of you. Would you have 10 min this week, or know who's best to point me to?

Count the characters of the actual draft before presenting it. If it's over 200, cut the ask down to its shortest form before cutting context — the ask is what the reader needs to act on.

## Format 2: Email

**Subject line:** clear and specific — role title and your name, e.g. "R&D Hardware Engineer application — Brendan Hlibok" — never a generic "Application" or "Inquiry."

**Length:** roughly 150-250 words. Shorter than the old cover-letter budget; email is scanned, not read closely, and a wall of text works against you here more than in a formal letter.

**Structure:** greeting → one sentence on the role and why you're reaching out → 2-3 sentences (or a short bullet list) on the most relevant experience, matched to the posting → one sentence on next steps or availability → sign-off. Mention the CV as an attached/linked Google Doc; don't restate its contents at length — the email is the pitch, the CV is the evidence.

**Plain text, no Doc.** An email draft is delivered as the message body — write it directly, no separate file needed beyond the archive record described in `/apply`.

## Format 3: Cover Letter (Google Doc)

Used when a posting or portal explicitly expects one, or as the safe default when no other signal points elsewhere. Drafted into a **fresh Google Doc each time** — there's no single reusable base document the way the CV has one (a cover letter's content is bespoke per application by nature), but the visual shape should stay consistent across applications: match the pattern seen in the "Outreach Examples" folder's past cover letters (name/contact header, paragraph body, closing/signature block) so a new one doesn't look like a one-off improvisation.

### Salutation
- If you know the hiring manager's name: "Dear [First Last],"
- If you know the team: "Dear [Company] hiring team,"
- Generic: "Dear [Company]," (avoid "To whom it may concern")

### Length — Hard 1-Page Limit
- Target: 1 page including signature block
- Maximum: **never exceed 1 page**
- **Word budget: 250-300 words** of body text. This is the safe maximum; 350 words will overflow a normal one-page layout.
- **Always count**: opening paragraph + a body paragraph (with an optional short bullet list of concrete achievements) + closing paragraph = 3 blocks. Add a 4th only if the others are short.
- When adding company-specific content, trim other content to compensate rather than adding net length.

### Structure
1. Opening — role, connection to background, 2-3 sentences.
2. Body — most relevant experience; a short 3-bullet list of concrete achievements works well here and reads faster than a dense paragraph.
3. Company-specific paragraph — why this role, why this company specifically, referencing something real (mission, product, recent news) rather than a generic compliment.
4. Personal-fit paragraph — behavioral strengths, team contribution, 2-3 sentences.
5. Closing — brief, forward-looking, then sign-off.

### Export-and-Inspect Loop (MANDATORY)
Same discipline as the CV: after drafting, export the Doc to PDF (Drive API `files.export`) and Read it to visually confirm it fits on exactly 1 page with the signature block intact and nothing cut off. If it overflows, trim content per the word budget above — there's no LaTeX-style rescue lever here, so stay well under budget rather than editing right up to the edge.

### Non-English Cover Letters
- Same structure, just write content in the posting's language
- Adjust date format to local convention
- Adjust closing to local convention (e.g. "Med venlig hilsen," for Danish)

## Writing Rules That Apply to All Three Formats
- No em-dashes (use commas or periods instead)
- No cliches or empty filler
- Every claim backed by a specific example
- Forward-looking framing: focuses on tasks you'll solve, not just past duties
- Company name and role are correct throughout
- Language matches the job posting's language

## Checklist Before Finalizing

**LinkedIn message:**
- [ ] Character count is ≤200 (counted, not estimated)
- [ ] One clear ask, no pitch/bullet list
- [ ] Addressed to an actual 1st-degree connection, not a cold contact

**Email:**
- [ ] Subject line names the role and your name
- [ ] 150-250 words
- [ ] CV mentioned as attached/linked, not restated
- [ ] Professional but not stiff tone, matched to the company's formality

**Cover letter:**
- [ ] Fits on exactly one page after export-and-inspect
- [ ] Motivation paragraph references this specific company's mission/values
- [ ] Date is current
- [ ] Salutation is appropriate (named person if possible)
- [ ] Opening line is engaging and specific, not generic

## Submission Guidelines (Best Practice)
- Submit only what the format calls for: LinkedIn message stays in LinkedIn, email stays as an email, cover letter Doc gets shared/exported per the employer's instructions
- For a cover-letter Doc that needs to leave Google Docs, export as PDF to preserve formatting
- Follow all employer instructions regarding anonymity or specific materials
