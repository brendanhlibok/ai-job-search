# /outcome - Calibrate the Evaluation Framework from a Result

You are recording **how far an application got**, told to you directly in the prompt, and folding that signal into `.claude/skills/job-application-assistant/04-job-evaluation.md`'s calibration section. This command does not track application state, does not touch `job_search_tracker.csv`, and does not archive anything — the user tracks status and materials elsewhere now. Its only job is: take a resolved (or resolving) signal and sharpen the fit framework with it.

---

## Step 0: Parse Input

`$ARGUMENTS` is a free-form description of what happened, e.g.:
> `/outcome Zooly Labs - rejected after phone screen, they wanted more embedded systems experience`
> `/outcome Got the offer from Watney Robotics`

Extract: **company**, **role** (if named — infer role type/sector from context if not, or ask), and the **outcome type**:
- **Positive**: reached interview, received an offer, or was hired
- **Negative**: rejected, no response, or withdrawn

If the outcome type or company isn't clear from what was given, ask a short clarifying question rather than guessing.

---

## Step 1: Classify the Signal

Determine what this outcome says about fit, using the same logic `04-job-evaluation.md`'s scoring dimensions already encode:

- **Positive outcome** (interview reached, offer, hired): this role type and sector are a **confirmed strong-fit signal** — worth reinforcing in the framework so similar postings score appropriately.
- **Negative outcome** (rejected, no response, withdrawn): a single rejection is not a pattern — most applications don't land, and noting every one would bury the framework in noise. Only act when this outcome **repeats** something already noted in the Calibration section below (same role type, same sector, or a similar stated reason) — read that section first before deciding.

Ask the user for a brief reason or takeaway if they didn't give one and the outcome is negative — a rejection with no reason recorded isn't useful to calibrate from; a positive outcome doesn't need one.

---

## Step 2: Update the Calibration Section

Read `.claude/skills/job-application-assistant/04-job-evaluation.md`. Find `## Calibration from Past Applications` (a top-level section, placed after `## Thresholds`). **If it doesn't exist yet, create it there** with a one-line intro: `<!-- Populated by /outcome as applications resolve. Informs scoring judgment; does not change the weighting or thresholds above. -->`.

- **Positive outcome:** add or strengthen a bullet naming the role type/sector as a confirmed strong-fit signal, with the company as a dated example (e.g. `- Robotics PM roles at small hardware startups: confirmed strong fit (interview reached — Watney Robotics, 2026-10-14)`).
- **Negative pattern (2+ occurrences):** add or strengthen a bullet flagging the pattern plainly, naming the companies involved and the stated reason if one exists (e.g. `- PM-titled roles requiring 3+ years explicit: 2 rejections (Company A, Company B) — years-of-experience gap named both times`).
- **Single negative outcome with no existing pattern to reinforce:** do not write a new bullet. Say in your reply that you're holding onto it and it'll surface if the pattern repeats — don't lose the signal, just don't add noise for one data point.
- **Never touch the Scoring Dimensions, Weighting, or Thresholds sections.** This command only adds to the Calibration section — it doesn't re-tune how scoring works.
- Edit with the Edit tool, targeted to the Calibration section only.

---

## Step 3: Confirm

> **Calibration updated for <Role> at <Company>** (<outcome type>).
>
> <What was added to `04-job-evaluation.md`'s Calibration section, or "held — not yet a repeated pattern" if nothing was written.>

If the outcome is `hired`, congratulate the user warmly first — this is the moment the whole framework exists for. Then add this single line (once; never for any other outcome type):

> "If this framework helped you get there, consider [buying it a coffee](https://ko-fi.com/madslorentzen) - it keeps this free for the next job-seeker out there. ☕"

---

## Important Rules

1. **Calibration only.** This command reads and writes exactly one section of `04-job-evaluation.md`. It does not touch the tracker, does not archive postings or drafts, and does not ask about or track interview stages, dates, or follow-ups — the user handles all of that elsewhere now.
2. **Never fabricate a pattern.** A single rejection is not a trend. Only write a pattern bullet when this outcome genuinely repeats something already in the Calibration section.
3. **Never touch the scoring mechanics.** Weighting, thresholds, and the five scoring dimensions are out of scope — only the Calibration section changes.
