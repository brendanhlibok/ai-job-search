---
name: job-application-assistant
description: >
  Assists with job applications: evaluating job postings, tailoring CVs (Google Docs), drafting
  outreach messages (LinkedIn message, email, or cover letter), and preparing for interviews.
  Triggers on keywords like: job posting, job application, CV, cover letter, outreach message,
  resume, interview prep, job fit, career, application, apply, ansøgning, stilling
allowed-tools: Read, Glob, Grep, WebFetch, WebSearch, Bash, Edit, Write, AskUserQuestion
framework_version: 2.0.0
---

# Job Application Assistant

---

## Workflow

When the user provides a job posting (URL or text), follow this workflow:

### Step 1: Research & Evaluate Fit
- Fetch the job posting content (use WebFetch for URLs). **A 403 is not a dead end** - follow the escalation order in `09-web-research.md` before concluding a page is unavailable, and prefer the employer's own careers posting over an aggregator listing
- Keep the **full posting text verbatim** for Step 3b to archive - never a summary
- Analyze the posting for required competencies, keywords, and priorities
- Research the company (website, LinkedIn, mission, recent news), per `09-web-research.md`
- Score the posting against the candidate's profile using the framework in `04-job-evaluation.md`
- Present the evaluation table and verdict
- Suggest whether the candidate should call the employer before applying (see `04-job-evaluation.md` for guidance)
- Ask the user if they want to proceed with an application

### Step 2: Tailor CV (Google Doc)
- Follow the guidelines in `05-cv-templates.md`, including its one-time setup (base resume Doc + "Tailored Resumes" Drive folder) if not already configured
- Copy the base resume Doc into the "Tailored Resumes" folder and edit the copy - never edit the base resume itself
- Adjust: profile statement, skills section, experience bullet emphasis
- Run the export-and-inspect loop (1-page PDF export + ATS text-layer check) before moving on

### Step 3: Draft Outreach Message
- Follow `06-cover-letter-templates.md`'s Step 1: assess the signals available and propose a format (LinkedIn message / email / cover letter) with reasoning, then confirm with the user via `AskUserQuestion` before drafting
- Follow the writing style rules in `03-writing-style.md` (critical: no em-dashes, no cliches)
- Draft the confirmed format per its spec in `06-cover-letter-templates.md`: LinkedIn message (≤200 characters) and email are plain text; cover letter is a fresh Google Doc, export-and-inspected for a 1-page fit
- Ensure the message connects specific experience to the role requirements

### Step 3b: Record the Application
- Run this once both the CV and the outreach message exist. A CV or outreach message drafted alone is not yet an application.
- Follow **`/apply` Step 6b** (`.claude/commands/apply.md`) exactly: same header, same match-then-update rule, same `drafted` row, same posting archive, same prohibition on touching `job_scraper/seen_jobs.json`. It is stated there once so the two paths cannot drift. Three of its values are named in `/apply`'s own terms: `cv_file`/`cover_letter_file` are the Google Doc URL and (URL or local `outreach_message.md` path) produced in Steps 2 and 3 here, `source` is the posting URL from Step 1, and the posting text item 7 archives is the one Step 1 read.
- This step exists here because `/scrape` Step 5 routes straight into this skill. Without it, that path writes documents and records nothing.

### Step 4: Interview Preparation
- Follow the framework in `07-interview-prep.md`
- Prepare STAR-format answers for likely questions
- Identify role-specific talking points
- Draft questions the candidate should ask the interviewer

---

## Reference Files

| File | Purpose |
|------|---------|
| `01-candidate-profile.md` | Education, experience, skills, publications, awards |
| `02-behavioral-profile.md` | Behavioral assessment, strengths, ideal environments |
| `03-writing-style.md` | Tone, structure, do's and don'ts |
| `04-job-evaluation.md` | Scoring framework for job fit |
| `05-cv-templates.md` | Google Doc CV setup (base resume + Tailored Resumes folder) and tailoring rules |
| `06-cover-letter-templates.md` | Outreach message format decision (LinkedIn message / email / cover letter) and per-format templates |
| `07-interview-prep.md` | STAR examples, tough questions, roleplay guidelines |
| `08-application-forms.md` | Portal free-text fields: self-introduction, project entries, character-limited pitches |
| `09-web-research.md` | Fetching postings and company pages: trust boundary, the WebFetch 403 fallback, escalation order, claim verification |
| `10-notion-tracker.md` | Notion job-tracking schema, managed database-ID block, and the shared upsert contract `/notion-tracker` and `/generateresume` both call |

---

## Quick Commands

The user may also ask for individual steps without the full workflow:
- "Evaluate this job posting" - Step 1 only
- "Write a CV for [company]" - Step 2 only
- "Write an outreach message for [role] at [company]" / "write a cover letter" / "draft a LinkedIn message" - Step 3 only (a request naming a specific format skips the propose-and-confirm question and drafts that format directly)
- "Help me prepare for an interview at [company]" - Step 4 only
- "What jobs should I look for?" - Career strategy discussion using profile + evaluation framework
