# Meridian research wiki: design spec

Date: 2026-09-20
Status: Draft for review
Pattern: LLM wiki (https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
Related: `raw/client-brief.md`, `data-handling-checklist.md`

## 1. Purpose

Prepare for the one-on-one stakeholder interview with Dana Okafor (VP of Operations, Meridian Markets). The wiki is an LLM-maintained, compounding knowledge base with two jobs, in priority order:

1. **Sharpen interview questions.** Surface gaps, ambiguities, and contradictions in the brief and produce a prioritized question list.
2. **Build domain knowledge.** Specialty grocery, store expansion and site selection, and loyalty analytics, so the team can speak credibly.

Dana travels Tuesdays and Wednesdays and replies slowly, so the interview is likely a single, time-limited opportunity. Question priority matters more than coverage.

## 2. Scope and constraints

- **Sources:** public industry research and the client brief only. No client extract data (POS, loyalty, labor) ever enters the repo or the wiki.
- **Data rules:** the wiki is maintained by an AI tool, so it is bound by `data-handling-checklist.md`. Only **Allowed**-tier material may be placed in `raw/`.
- **Location:** `wiki/` in this repo, versioned in git. Browsable in VS Code; Obsidian is optional.
- **Out of scope:** search tooling (qmd or similar), Obsidian plugins, and any dashboard or analysis work.

## 3. Architecture

Three layers, per the pattern:

| Layer | Location | Owner | Rule |
|---|---|---|---|
| Raw sources | `raw/` | Team curates | Immutable; the LLM reads, never edits |
| Wiki | `wiki/` | LLM | Created and maintained by the LLM; every claim cites a source page |
| Schema | `CLAUDE.md` | Team + LLM | Conventions, templates, workflows, data rules |

### 3.1 Directory layout

```
raw/                      immutable sources; Allowed-tier only
  client-brief.md
wiki/
  index.md                catalog by category, one-line summary per page
  log.md                  append-only operation log
  sources/                one summary page per raw source
  entities/               Meridian Markets, Dana Okafor, Marcus (IT), Pasadena, competitors
  concepts/               site selection, same-store sales, category mix, loyalty analytics,
                          store maturity curves, cannibalization
  synthesis/
    open-questions.md     gaps and ambiguities from the brief, ranked
    assumptions.md        assumptions and how to test each in the interview
    interview-guide.md    prioritized questions for Dana, with supporting evidence
CLAUDE.md                 the schema
```

### 3.2 Page conventions

- **Frontmatter:** `type` (source | entity | concept | synthesis), `sources` (list of source pages), `updated` (date), `confidence` (low | medium | high).
- **Links:** `[[wikilinks]]` between pages.
- **Citations:** every factual claim links to a page in `sources/`. Claims from the brief cite `sources/client-brief`.
- **Log entries:** `## [YYYY-MM-DD] ingest | query | lint | <title>`.
- **Index:** updated on every ingest and every new page.

## 4. Operations

> Drafted by Claude from the pattern; not yet reviewed in the interview. Please review closely.

### 4.1 Ingest

1. **Data gate.** Confirm the source is public or the client brief, assign its tier per the checklist, and record the tier in the source page frontmatter and the log entry. If the tier is not Allowed, stop and do not read the file.
2. Read the source and discuss key takeaways with the user.
3. Write a `sources/` summary page.
4. Create or update affected entity and concept pages, and add cross-links.
5. Update `open-questions.md` and `assumptions.md` where the source raises or resolves items.
6. Update `index.md` and append to `log.md`.

The first ingest is `raw/client-brief.md`, which seeds the entity pages (Meridian, Dana, Marcus, Pasadena) and the initial open questions.

### 4.2 Query

Answer questions from the wiki with citations. Worthwhile answers are filed back as concept or synthesis pages and logged.

### 4.3 Lint

Periodic pass that reports, and fixes on approval:

- contradictions between pages
- stale claims superseded by newer sources
- orphan pages with no inbound links
- missing cross-references or missing `sources` citations
- knowledge gaps to investigate next
- any content that looks like restricted client data (customer or employee records)

## 5. Interview guide

> Drafted by Claude; not yet reviewed in the interview.

`synthesis/interview-guide.md` is the primary deliverable. Each entry has:

- the question, phrased for Dana
- why it matters (the decision it informs)
- supporting evidence (links to sources and concepts)
- priority (must ask | should ask | if time)

Order the guide so the must-ask questions fit in a short session. Seed themes from the brief: the Pasadena assumption, how success is defined ("increase revenue, reduce costs, improve customer experience" is broad), uneven store performance, use of the loyalty data within the AI-tool restriction, POS migration effects on data continuity, and the board timeline in three weeks.

## 6. Definition of ready for the interview

- [ ] Client brief and the planned public research sources are ingested, each with a recorded tier
- [ ] `open-questions.md` is ranked
- [ ] Every assumption in `assumptions.md` has a linked interview question or is marked as testable another way
- [ ] `interview-guide.md` has its must-ask questions, each with cited evidence
- [ ] A lint pass has been run and its findings resolved
- [ ] Nothing in `raw/` or `wiki/` is Restricted or Ask-first data

## 7. Open items

- The exact list of public research sources is not yet chosen.
- Sections 4 and 5 were drafted without interview input and need your review.
- Optional tooling (Obsidian, search) is deferred.
