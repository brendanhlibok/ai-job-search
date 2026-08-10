---
framework_version: 2.0.0
---

# CV Templates and Tailoring Guide

<!-- SETUP: Profile statements and section ordering are personalized by running /setup -->

<!-- BEGIN ACTIVE-GOOGLE-DOC-TEMPLATE (set once during one-time setup - do not edit by hand) -->
> **Base resume:** *not yet configured* — see "One-Time Setup" below.
> **Output folder:** *not yet configured*
<!-- END ACTIVE-GOOGLE-DOC-TEMPLATE -->

## Medium: Google Docs

CVs are Google Docs, not LaTeX/PDF. There is no compile step and no per-application document class — every tailored CV is a copy of your one real base resume, edited in place. This is different from the old LaTeX workflow in one important way: the visual template (fonts, margins, header layout, section styling) is never authored by Claude. It's whatever your base resume already looks like. Claude's job is narrower and more repeatable: copy it, then edit the words.

## One-Time Setup

Before the first CV can be tailored, this file needs two things recorded in the `ACTIVE-GOOGLE-DOC-TEMPLATE` block above:

1. **Base resume Doc URL.** Ask for the Google Doc URL of the comprehensive resume you already use as your starting point for every application (the equivalent of the old `cv/main_example.tex` "master reference" — the most complete, unedited version of your professional record). Fetch it once (Docs MCP `get_document` or equivalent) to confirm access and to learn its actual section structure — heading text, section order, whether it has a summary/objective line. Do not assume it matches the section names used elsewhere in this file; read what's actually there.
2. **Output folder.** Confirm a Google Drive folder named **"Tailored Resumes"** exists (Drive MCP `list`/`search`, filtering to folders you own). If it doesn't exist, create it (Drive MCP `create` with the folder mimetype) and confirm the name with the user before treating it as final — folder names are visible in their Drive, unlike a `cv/` directory nobody but you ever opens.

Write both into the managed block: the Doc URL (and ID, parsed from the URL) for the base resume, and the folder name plus ID for the output folder. `/apply` Step 2 checks for this block before drafting a CV and runs this setup automatically the first time it's missing.

**The base resume is never edited.** Every write operation targets a *copy* of it, never the original.

## Per-Application Workflow

1. **Copy.** Use the Drive API's copy operation (`files.copy` semantics) to duplicate the base resume Doc into the "Tailored Resumes" folder. Name the copy `<Company> - <Role>` (e.g. `Pendar Technologies - R&D Hardware Engineer`) — human-readable, since this folder is something you'll actually browse in Drive, unlike the old `cv/main_<company>_<role>.tex` path.
2. **Read the copy's structure.** Fetch the new Doc's content (`get_document`) and locate its actual sections by heading text — Summary/Profile (if present), Skills/Core Competencies, Experience, Education, and so on. Confirm you're editing the right ranges before writing.
3. **Edit in place, don't rebuild.** Use `batchUpdate` requests (`replaceAllText`, or `deleteContentRange` + `insertText` at a specific location) to swap in tailored wording *within* the existing formatted structure. Inserting text at an existing run generally inherits that run's formatting (font, bold, bullet style) automatically — this is the main advantage of editing a real formatted document instead of generating one from scratch, and it's why step 2 matters: edit inside the existing bullet/paragraph, don't delete-and-recreate it, or you risk losing the formatting that made the base resume look right in the first place.
4. **Reordering sections is a heavier operation** (moving content ranges, not just replacing text within them) and should be avoided unless the role genuinely calls for it — prefer re-emphasizing content within the existing order first.

## Section-by-Section Tailoring

The tactical guidance below carries over unchanged from principle to principle — only the mechanism (Docs API edit vs. LaTeX source edit) changed. Apply it to whatever sections your actual base resume has; don't force it into section names it doesn't use.

### Profile Statement / Elevator Pitch (Best Practice)
If your base resume has one, this is the highest-value thing to customize per application: a concise, compelling 1-3 sentence introduction explaining why you're qualified for *this specific role*, focused on what the employer gains from hiring you.

When the role sits outside your home domain, **lead with the domain-transfer argument** — the sentence connecting your background to their problem belongs at the opening, not buried later. It's the strongest card a domain-changer holds; play it first.

**Create 2-3 profile statement variants for your main role types:**

<!-- SETUP: These are populated based on your background -->
**For [YOUR_PRIMARY_ROLE_TYPE] roles:**
> [YOUR_PROFILE_STATEMENT_TEMPLATE_1]

**For [YOUR_SECONDARY_ROLE_TYPE] roles:**
> [YOUR_PROFILE_STATEMENT_TEMPLATE_2]

Statements labeled *[Used for: <company>_<role>]* were extracted from archived application drafts by `/setup` Path A. They are **phrasing references, never fact sources**: when drafting from one, every factual claim still comes from `01-candidate-profile.md` — a past tailored draft does not vouch for its own accuracy.

### Core Competencies / Skills Section (Best Practice)
Reorder and emphasize based on the role, within whatever format the base resume already uses for this section.

Use the posting's own core term when it truthfully applies — ATS and skim-reading hiring managers match literally, and "MLOps" outperforms a paraphrase like "ML Deployment".

### Education
- Always include your highest degrees
- For senior roles, keep education brief (dates and titles only)
- Include thesis topics when relevant to the target role

#### In-progress qualifications must say so explicitly

**A bare year range is not enough.** An entry reading `2025–2026`, seen partway through 2026, looks like a *finished* degree, because a reader skimming a CV treats a closed range as closed. A profile statement that says "currently completing…" does not fix it: the education entry is where a reader checks the credential, so it has to stand on its own. State completion inside the entry itself — `In progress, expected [Month Year].` or `Expected completion [Month Year].` or a date field of `2025–present` — any consistent form works.

Claiming a credential not yet held is a factual misstatement, and it is the kind discovered at transcript or reference check rather than at interview. It costs nothing to prevent. The same applies to in-progress certifications and courses.

**Check for agreement:** for a current student, the profile statement, the education entry, and any availability or work-permit note must all give the same completion date. Contradiction between them is worse than any single version.

### Professional Experience
- Rewrite bullet points to emphasize aspects most relevant to the target role
- **Emphasize measurable results** where possible: "Reduced processing time by X%", "Model adopted by the team"

#### Check tenure against visible output

Before finalizing, look at each role the way a stranger will: **date span versus how much work is shown.** A two-year role represented by a single project reads as low output, whether or not that is fair. The reader cannot know what filled the time, so they guess, and the guess is unflattering.

This bites hardest on **career changers** (part of the tenure went into learning the new field), on **long-cycle work** (industrial deployment, clinical or regulatory projects, research — one delivery genuinely takes quarters), and on anyone whose employer kept them on a single account or product.

Three honest fixes, in order of preference:

1. **Surface more real work.** Ask what else the period contained. There are often real secondary projects, internal tooling, or support work that never reached the CV because it felt minor. Best fix when the material exists.
2. **Make the phases within the role explicit.** If the span genuinely had stages, say so — an initial period learning the domain or supporting the team, then ownership of the named work through to delivery. A phased arc reads as a growth curve; an undifferentiated multi-year block reads as stagnation.
3. **Name what made the cycle long.** Data collection from a live environment, validation with domain experts, deployment and iteration against real output. Reviewers who know the domain accept this immediately.

**Never** pad with invented projects, and **never** quietly shorten the employment dates so the ratio looks better. Both are discoverable, and both are worse than the perception problem being solved.

**Prepare the interview answer too.** If a long span against little visible output survives these fixes, the question is coming. The candidate needs a ready two-part answer — what actually filled the time, and what the outcome was — recorded in their interview prep rather than improvised in the room.

### Handling Employment Gaps (Best Practice)
If there is a gap in your employment history:
- The gap should be explained matter-of-factly if needed
- Describe how professional development continued during the gap
- Frame as deliberate skill-building and career repositioning

### Publications
- Include Google Scholar link if applicable
- Select the most relevant publications (not always all of them)
- For non-academic roles, keep brief

### Evidence Links
Wherever the CV names a verifiable artifact — a public project, a hackathon entry, a publication — carry its link so a reader can verify the claim in one click. A CV whose strongest claims are checkable reads as more credible everywhere else too.

### Honors and Awards
- Keep format brief, one line each

### References
- List references with name, title, company, and contact, or close with "More references are available upon request."
- **Do not attach reference letters** — employers typically contact references directly

## Export-and-Inspect Loop (MANDATORY)

Google Docs has no LaTeX-style exact page-break control — there's no `\needspace` or `\enlargethispage` equivalent. The only lever is content length within the base resume's existing formatting, so the discipline shifts from "fine-tune up to the edge" to "stay comfortably under budget." After editing the copy and before presenting it to the user:

1. Export the Doc to PDF (Drive API `files.export`, `mimeType=application/pdf`).
2. Read the exported PDF via the Read tool and visually inspect it: exactly 1 page, no cut-off text, no leftover placeholder tokens from the base resume that don't apply to this application, formatting intact (bullets, bold, spacing look like the base resume's, not broken by an edit).
3. If it overflows: trim content (see "Relevance-weighted cutting" below), re-export, re-check. Iterate until it passes.
4. If content looks thin (finishes well before the page ends): restore the highest-relevance item that was previously cut, if any — a CV that ends with obvious empty space looks incomplete.

## ATS Parseability

Most employers run CVs through an ATS before a human sees them, and the ATS reads the exported PDF's embedded **text layer**, not the rendered page. After the export-and-inspect loop passes, verify the text layer:

```bash
pdftotext -layout <exported-file>.pdf <exported-file>.txt
```

`pdftotext` comes from [poppler](https://poppler.freedesktop.org/) and is an **optional** dependency. If it is not installed, skip the mechanical check with a warning and rely on the visual PDF read for keyword coverage.

What to check in the extraction:

- **Contact details as literal text.** If the base resume uses icon glyphs for phone/email/LinkedIn, the failure mode is a contact detail carried *only* by an icon or a hyperlink label (link text with no visible URL): invisible to an ATS. The email address must always appear as printed text somewhere in the extraction.
- **No garbled output.** `(cid:NNN)` markers or `�` characters mean a font is embedded without a Unicode mapping — an ATS sees the same garbage. Unlikely with Google Docs' standard export fonts, but check anyway.
- **Reading order.** Multi-column layouts or text boxes in the base resume can interleave unrelated lines on export; if extraction order is scrambled relative to visual order, flag it to the user — this is a property of the base resume's own layout, not something a per-application edit can fix.
- **Keyword coverage.** Match the posting's required/preferred terms against the extracted text, in the posting's language. Prefer the posting's exact term over a synonym when it is truthfully applicable — ATS matching is often literal. Never add a keyword the profile does not support.

### Watch for autocorrected dashes (the Google Docs equivalent of the LaTeX en-dash bug)

The old LaTeX template had a confirmed failure mode where `--` ligatured into an en-dash and broke Workday's date parsing on import — a CV could pass every visual and text-layer check and still silently lose an end date or an entire education entry on import, discovered only while filling in the application form. Google Docs has the same risk from a different cause: its **Substitutions autocorrect** (Tools → Preferences → Substitutions) turns a typed hyphen between numbers into an en-dash by default. If a date range in the copy was typed or edited during tailoring, check the exported text layer shows a plain ASCII hyphen (`2022-2024`), not `2022–2024`. If autocorrect has already converted one, fix it directly in the Doc (Docs API edits are not subject to the client-side autocorrect that produced it, so a `replaceAllText` fix holds) and re-export to confirm.

A bare single year (`2024`) has the same problem as before — it gives an ATS parser no end date. Use an explicit range, with months for anything under a year (`Mar 2024 - Jul 2024`).

**Add this to `/apply`'s mandatory verification step**: after extracting the text layer, confirm every experience entry shows a start *and* an end separated by an ASCII hyphen.

## Page Budget — Hard 1-Page Limit

The CV **must** fit on exactly 1 page when exported. Use these content limits as a guide, adapted to whatever sections your base resume actually has:

| Section | Max budget |
|---------|-----------|
| Profile statement (if present) | 2-3 lines |
| Skills | 5 items, each 1-2 lines |
| Most recent role | 4-5 bullets |
| Previous role | 2-3 bullets |
| Older roles | 2 bullets (1 line each) |
| Education | 2-3 entries |
| Publications | 2-3 entries |
| Awards | 3 entries, single line each |
| References | "Available upon request." (single line) |

**If in doubt, cut rather than squeeze.** There's no font-scale or margin lever to reach for here (that would mean editing the base resume's own styling, which is out of scope for a per-application tailoring pass) — cut content instead.

## Relevance-weighted cutting (the right way to shrink a CV)

**Cut by signal, not by section.** Static priority lists ("remove oldest education first, then shorten the earliest role...") are wrong when a relevant "lower-priority" item is competing with an irrelevant "higher-priority" item. An older-role bullet that speaks directly to the posting is worth more than a recent-role bullet that does not.

For every candidate line, score three things:

1. **Relevance to THIS posting** — does the line hit a named tool, keyword, or stated responsibility in the job ad?
2. **Uniqueness** — is it the only place this claim appears, or is it duplicated elsewhere in the CV?
3. **Narrative load** — does the outreach message depend on it? If cutting the line would force you to rewrite a cover-letter paragraph or the pitch in a LinkedIn message, it is load-bearing.

Cut the lowest-total-score line first, regardless of which section it sits in.

### Practical order of cuts (easiest → last resort)

1. **Redundancy.** If an achievement appears in both Core Competencies AND a role bullet, the Core Competencies version is usually the cleaner cut (the experience bullet is more concrete evidence).
2. **Profile-statement fluff.** A sentence that just restates what Publications or Skills will show.
3. **Low-relevance experience bullets.** A bullet about work that does not touch posting keywords, wherever it sits. This cuts across sections before touching the structural list.
4. **Low-relevance supporting content.** An older-role bullet that does not speak to the target role. A certification that does not touch the posting's stack. A language entry that can be condensed to one line.
5. **Low-relevance publications.** Keep 1-2 publications that best match the posting. Cut the rest before touching experience bullets.
6. **Last-resort structural cuts.** Oldest education entry, tightening an older role to 2 bullets, collapsing Certifications into a single line. These only happen if the relevance-weighted cuts above have already been exhausted.

### Pitfalls to avoid

- Do not mechanically cut from the bottom of a static section list without checking relevance. "Cut the oldest role first" is wrong if that role is literally about the skill the posting asks for.
- Do not cut the one concrete example the outreach message leans on. Relevance is measured against the outreach message you wrote, not just the job posting — a reader who gets both will have read both.
- Do not restructure the base resume's own section order or styling to fix an overflow — that's a change to the template itself, not a per-application tailoring decision. Flag it to the user instead of doing it silently.
