---
framework_version: 2.0.0
---

# CV Templates and Tailoring Guide

<!-- SETUP: Profile statements and section ordering are personalized by running /setup -->

<!-- BEGIN ACTIVE-GOOGLE-DOC-TEMPLATE (set once during one-time setup - do not edit by hand) -->
> **Reference folder:** "career/resumes" in Google Drive (ID: `1slC1PLgcb9nHUT3pvPNhPCS9_P5LAkd7`) — holds the Master Resume, all (BASE)/(OPERATIONS)/etc. forks, and the "Tailored Resumes" output subfolder. See "ACTIVE MEDIUM" below for the current reference documents.
> **Output folder:** "Tailored Resumes" (ID: `1SUfUep0Wp9Ki6kAsLrJk_ZAyvJQhsYv0`)
<!-- END ACTIVE-GOOGLE-DOC-TEMPLATE -->

## ACTIVE MEDIUM (as of 2026-09-29): Google Docs Reference System

Supersedes the 2026-08-11 "Structured Text" medium below (kept for history, no longer used — `templates/Brendan Resume Text` is stale and should not be read for tailoring). Google Drive access is back, and the user has clarified the real reference system that already lives there.

### Reference documents (read-only to Claude — see "Absolute rule" below)

All live in the "career/resumes" Drive folder:

- **Master Resume** (currently `Master Resume (Sept 2026)`, ID `1JRkwXjELn-AFXpAWJgJe49g1pT-u9X-BRLXquZBtBMY`) — "a complete encapsulation of every possible work experience... and every possible bullet." Organized by category (Management Experience, Hospitality Experience, Technical Experience, Personal Projects, Activities, Skills), **not** in normal resume layout — never copy it directly as a starting point, only pull bullet/phrasing options and information from it. It contains real facts not yet mirrored in `01-candidate-profile.md` (e.g. Pub at Rice bartending, Leelynn's Dining Room hosting, the Parkinson's Disease Tremor Simulator project, the ASL Recognition Band project, TEDx Speaker, extra MD Anderson bullets) — treat these as grounded once the user confirms syncing them into the profile; until then, verify with the user before using anything from Master that isn't already in `01-candidate-profile.md` / `CLAUDE.md`.
- **(BASE) forks** — the go-to engineering/product-design/robotics resume, used ~90% of the time. Multiple forks can exist simultaneously, named by whatever the user chooses (date, e.g. `(BASE) 2026-09-29`, or location, e.g. `Brendan_Hlibok (BASE) July 2026 - New York` for Northeast-targeted applications) — don't assume the naming convention, read what's actually there. **Always use the most recently created fork** as the tailoring starting point unless the user says otherwise.
- **(OPERATIONS) fork** (and any future domain-specific forks — event planning, hospitality, etc.) — precedent for the "new domain" workflow below. New sections, different structure, still pulling facts from Master.

### Absolute rule: references are read-only to Claude

**Never edit the Master Resume or any fork (BASE, OPERATIONS, or future ones) unless the user explicitly asks.** These are the user's own reference documents — they edit them; Claude reads from them. This holds even though the user has granted permission to write resume documents in general — that permission is scoped to *new tailored output copies* (below), not to the references. If a reference looks like it has an error (a wording drift, a stale fact), flag it to the user; do not fix it yourself.

### Two tailoring workflows

**A. Standard (~90% of the time) — engineering/product-design/robotics roles:**
1. Copy the most recent (BASE) fork into the "Tailored Resumes" folder (or the user's chosen equivalent) via `copy_file` — this preserves all formatting.
2. **Select, don't reword** (standing user instruction — see `/generateresume` Step 1). Choose which bullets to keep, drop, or pull in from the Master Resume against the target posting's language, optimizing for **ATS keyword match** — every included bullet keeps its exact existing wording, verbatim from the fork or Master. The first two experience entries matter most — they're what catch the reader's eye first, so lead with the strongest, most relevant *verbatim* bullets available.
3. Swap in a Master Resume bullet verbatim when the (BASE) fork doesn't already have the best-fitting bullet for this posting. Dropping a less-relevant fork bullet in favor of a more relevant Master one is a selection decision and always fine; combining two bullets' wording into a new sentence, or editing a bullet to use the posting's own term, is rewording and is not done here — it becomes a suggested text change presented to the user instead (see `/generateresume` Step 3/5).
4. Keep it grounded: every claim still traces to `01-candidate-profile.md` / `CLAUDE.md` / the Master Resume (once synced) / the chosen (BASE) fork — no fabrication, same as always. Verbatim selection makes this close to automatic, but still confirm nothing drifted.

**B. New domain (occasional) — roles meaningfully different from the usual engineering track (event planning, hospitality, etc.):**
1. This is **highly collaborative**, not a solo draft-and-present — work the structure through with the user rather than delivering a finished resume unprompted.
2. Pull facts from the Master Resume's full breadth (e.g. Hospitality Experience, KODA Camp Counselor) — new sections are expected and fine.
3. `Brendan_Hlibok (OPERATIONS) Sept 2026` is the precedent to look at for how a prior new-domain resume was structured.

**Both workflows:** check with the user afterward to refine before calling it final, and the result must be **under 1 page**.

### Mechanism (as of 2026-09-29: real in-place editing)

The **Google Docs** connector (separate from Google Drive — added 2026-09-29) is connected and provides real in-place Doc editing via `read_doc` / `update_doc` (`documents.batchUpdate`). Before the first edit of a session, read the `google-workspace` skill (`Skill` tool) and its `references/docs.md` in full — it covers index/revision mechanics, `replaceAllText` vs. positional edits, and the mandatory verify step. In practice:
1. `copy_file` (Drive) the chosen reference fork into the output location — still the only step that touches a Doc other than the new tailored-output copy, and it targets that copy only, never a reference.
2. Edit the new copy directly with `update_doc`. For a simple word/phrase swap confined to a unique span, `replaceAllText` needs no read and no indexes — the fastest path, used for both edits on the first tailored resume under this system (Mammoth Brands, 2026-09-29). For anything positional (inserting a new bullet, reordering), read the doc first with `read_doc` and follow `references/docs.md`'s index/revision-guard rules.
3. **Verify, every time**: export to PDF (Drive `download_file_content`, `exportMimeType: application/pdf`) and read it with the Read tool to confirm exactly 1 page, correct content, and intact formatting — this is the CLAUDE.md Verification Checklist's mandatory Google Doc check, and it's cheap now that edits land directly.
4. Report the changes made (old → new) so the user can see what happened, even though they no longer have to apply it themselves.

This supersedes the paste-in mechanism entirely — a tailored resume is now a completed Doc, not a set of instructions for the user to carry out.

### Coded Docs Pipeline: not needed
The previously-planned custom Google Cloud OAuth/service-account script is unnecessary now that the Google Docs connector provides this directly. Not building it.

## Superseded: Structured Text medium (2026-08-11 to 2026-09-29 — no longer used)

`templates/Brendan Resume Text` was the interim workaround before the Master/fork system above was known to Claude. It is now stale and should not be read for CV tailoring — the Google Docs Reference System above is authoritative. Kept only as history; do not maintain it going forward.

## Section-by-Section Tailoring

The tactical guidance below carries over unchanged from principle to principle — only the mechanism (Docs API edit vs. LaTeX source edit) changed. Apply it to whatever sections your actual base resume has; don't force it into section names it doesn't use.

### Profile Statement / Elevator Pitch (Best Practice)
If your base resume has one, this is the highest-value thing to customize per application: a concise, compelling 1-3 sentence introduction explaining why you're qualified for *this specific role*, focused on what the employer gains from hiring you.

**Select-don't-reword applies here too.** Pick whichever existing variant below (or in Master) fits the role best and use it verbatim — don't blend two variants or edit one's wording to match the posting. If none of the existing variants fit well, that's a gap: suggest new phrasing to the user per `/generateresume` Step 3/5 rather than writing a new sentence into the Doc.

When the role sits outside your home domain, **lead with the domain-transfer argument** — the sentence connecting your background to their problem belongs at the opening, not buried later. It's the strongest card a domain-changer holds; play it first.

**Create 2-3 profile statement variants for your main role types:**

**For Product Design Engineer roles:**
> Mechanical engineer (Rice University, cum laude, GPA 3.87) with hands-on product design experience from concept to functioning prototype - from a 1st-place-worldwide haptic wristband (ISCAS 2025) to a custom surgical end-effector fabricated for MD Anderson Cancer Center. Skilled across CAD (SolidWorks, Creo), rapid prototyping, and DFM/DFA, looking to bring that same concept-to-prototype ownership to a small product design team.

**For Mechatronics / Robotics Hardware roles:**
> Mechanical engineer with cross-disciplinary mechatronics experience spanning CAD-driven mechanical design, embedded electronics (ESP32, Arduino, custom PCBs via KiCAD), and field robotics operations at Rugged Robotics. Combines hands-on prototyping (3D printing, CNC, custom PCB fabrication) with Python/Bash scripting to diagnose and improve autonomous systems in the field.

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

## Export-and-Inspect Loop (MANDATORY for the Google Docs medium; N/A while the Structured Text medium is active)

There is no PDF to export from a `.txt` file, so this loop doesn't run under the active Structured Text medium — but the **Page Budget** guidance right below still matters, since bullet length carries over once the user pastes tailored text into the real Doc. Keep bullets within the same budget a human would eyeball as roughly one-line-per-bullet-equivalent to the original, and flag it if a tailored bullet has grown noticeably longer than what it replaced.

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
