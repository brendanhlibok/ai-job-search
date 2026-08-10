# Search Queries for Job Scraper

<!-- SETUP: Customize these queries based on your skills, target roles, and location -->

## Installed portal CLIs (primary for `/scrape`)

`/scrape` discovers every portal skill under `.agents/skills/*/SKILL.md` and runs its CLI first. Shipped country-agnostic CLIs include `linkedin-search` and `freehire-search`; Danish demos ship disabled (left off - not relevant to a US job search) and any skill added with `/add-portal` is included the same way. You do **not** need a matching `site:` line below for those CLIs to run.

No dedicated US job-board CLI is installed yet. Run `/add-portal` to scaffold one for a board like Indeed, ZipRecruiter, or Built In when ready - until then, the `site:` templates below serve as the WebSearch fallback for those boards.

The `site:` query templates in this file are the **WebSearch fallback** - for portals without a CLI, company career pages, or when a CLI fails.

**Language scope:** All queries are in English - the candidate's sole declared working language for job search (see CLAUDE.md Languages table; American Sign Language does not apply to written job-board queries).

## Search Sites

Primary:
- **linkedin.com/jobs** - LinkedIn job listings (filter: United States, target metro areas below); also covered by `linkedin-search` CLI
- **freehire.com** - covered by `freehire-search` CLI
- A dedicated general US job board (Indeed, ZipRecruiter, etc.) - not yet scaffolded, add via `/add-portal`

Secondary (company career pages via Google):
- Direct Google searches with `site:` filters for known target companies, including **Pump Studios** (flagged by the candidate as an ideal-fit example of the product design firms he's targeting)

## Query Categories

Queries are grouped by priority. All queries are in English. Combine each query with your target city (see Location Filter below) where the site supports it.

### Priority 1: Product Design Engineering

These match the candidate's strongest and most desired career direction: junior/entry-level product design roles at small firms, covering the full concept-to-market lifecycle.

```
site:linkedin.com/jobs "Product Design Engineer" [CITY]
site:linkedin.com/jobs "Mechanical Design Engineer" [CITY]
"Product Design Engineer" entry level OR junior [CITY]
"Mechanical Design Engineer" entry level OR junior [CITY]
```

### Priority 2: Mechatronics / Cross-Disciplinary Hardware Roles

Match the candidate's combined mechanical + electrical + embedded software background.

```
site:linkedin.com/jobs "Mechatronics Engineer" [CITY]
"SolidWorks" "KiCAD" engineer [CITY]
"product development engineer" prototyping [CITY]
```

### Priority 3: Adjacent Roles

Roles the candidate could pivot into that still build toward product design experience.

```
site:linkedin.com/jobs "Manufacturing Engineer" entry level [CITY]
site:linkedin.com/jobs "R&D Engineer" [CITY]
site:linkedin.com/jobs "Hardware Engineer" junior [CITY]
site:linkedin.com/jobs "NPI Engineer" [CITY]
```

### Priority 4: Broader Net

Wider search leveraging the candidate's current robotics/operations experience as a fallback direction.

```
site:linkedin.com/jobs "Robotics Engineer" entry level [CITY]
site:linkedin.com/jobs "Mechanical Engineer" entry level OR junior [CITY]
```

## Location Filter

The candidate is actively relocating away from Houston, TX. Must live in, or within ~30 minutes by car of, a major city; a big city with a young population is ideal, nature access and/or East Coast proximity to family is a plus.

- **Ideal** (city proper or within ~30 min by car): San Francisco, Seattle, New York City, Chicago, Los Angeles, Atlanta, Austin, Denver, Washington DC, Philadelphia, Boston
- **Acceptable**: broader metro areas surrounding the ideal cities above
- **Borderline**: other major US cities not on the list but with a similar young/urban/nature-access profile - judge case by case
- **Too far**: Houston, TX (current base - a role there would need to justify staying) and remote/rural areas without city access

## Language Filter

English only - the candidate's sole declared working language for job search purposes (see CLAUDE.md Languages table).

## Date Filter

Only include jobs posted within the last 14 days, or with an application deadline that has not yet passed. If a posting date cannot be determined, include it but flag as "date unknown".

## Adapting Queries

If the user specifies a focus area, select queries from the matching category and also generate 2-3 custom queries for that focus. For example:
- "/scrape [focus_area]" -> relevant category queries + custom focus-specific queries
