# Meridian Research Wiki Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build an LLM-maintained research wiki in `wiki/` that produces a prioritized interview guide for Dana Okafor.

**Architecture:** Three layers per the LLM wiki pattern: immutable `raw/` sources, an LLM-owned `wiki/` of markdown pages with `[[wikilinks]]`, and a `CLAUDE.md` schema that defines conventions, workflows, and the data gate. This is a documentation project, so "tests" are verification commands and manual checks, not code tests.

**Tech Stack:** Markdown, YAML frontmatter, git. Shell (bash) for verification commands. Optional: Obsidian or VS Code for browsing.

**Spec:** `docs/superpowers/specs/2026-09-20-meridian-research-wiki-design.md`

## Global Constraints

- Only **Allowed**-tier material may enter `raw/` (per `data-handling-checklist.md`). No Meridian POS, loyalty, or labor data, and no excerpts or aggregates of them, anywhere in the repo.
- `raw/` is immutable: the LLM reads it and never edits it.
- Every factual claim in `wiki/` cites a page in `wiki/sources/`.
- Page frontmatter fields: `type` (source | entity | concept | synthesis), `sources` (list), `updated` (YYYY-MM-DD), `confidence` (low | medium | high).
- Log entry format: `## [YYYY-MM-DD] ingest | query | lint | <title>`.
- `index.md` is updated on every ingest and every new page.
- Page file names are unique across all wiki folders (wikilinks use the bare file name).
- Public sources: the user finds them and adds them to `raw/`. Claude does not search for or add sources unless the user asks. Claude only ingests what the user has placed in `raw/`.
- Commits: one per task, and only after the user approves. Nothing is committed without a yes.
- Source topics in scope: store site selection, specialty grocery market, retail performance metrics.

## Choices made with you (from the spec's open items)

| Open item | Decision |
|---|---|
| Public research topics | Store site selection, specialty grocery market, retail performance metrics |
| Who adds sources | You find and add them to `raw/`; Claude only ingests (changed 2026-09-20 from "Claude proposes, you approve") |
| Commits | Per task, you approve each |

## Progress (updated 2026-09-20)

| Task | Status |
|---|---|
| 1. Foundation and schema | Complete (`b0d32ac`) |
| 2. Ingest the client brief | Complete (`342cd73`) |
| 3. You add the public sources | Complete (revised approach) |
| 4. Ingest the public sources you added | In progress (3 sources ingested) |
| 5. Concept pages and synthesis | Not started |
| 6. Interview guide | Not started |
| 7. Lint and readiness check | Not started |

Outside the plan: a `gelsons` entity page was filed from a query on where specialty grocers are opening (logged as a `query` entry). `raw/client-brief.md` is gitignored and stays local only.

## File structure

| Path | Responsibility |
|---|---|
| `CLAUDE.md` | Schema: conventions, page templates, ingest/query/lint workflows, data gate |
| `wiki/index.md` | Catalog of every page, by category |
| `wiki/log.md` | Append-only operation log |
| `wiki/sources/*.md` | One summary page per raw source |
| `wiki/entities/*.md` | Meridian Markets, Dana Okafor, Marcus, Pasadena |
| `wiki/concepts/*.md` | Site selection, same-store sales, sales per square foot, category mix, store maturity, cannibalization, specialty grocery landscape |
| `wiki/synthesis/open-questions.md` | Ranked gaps and ambiguities |
| `wiki/synthesis/assumptions.md` | Assumptions, each linked to a question or another test |
| `wiki/synthesis/interview-guide.md` | The deliverable |
| `raw/*.md` | Immutable sources (brief plus public sources you add) |

---

### Task 1: Foundation and schema

**Status:** Complete. Committed as `b0d32ac`.

**Files:**
- Create: `CLAUDE.md`, `wiki/index.md`, `wiki/log.md`, and empty folders `wiki/sources/`, `wiki/entities/`, `wiki/concepts/`, `wiki/synthesis/` (each with a `.gitkeep`)
- Existing, to be committed: `raw/client-brief.md`, `data-handling-checklist.md`, the spec, and this plan

**Interfaces:**
- Produces: the conventions every later task follows (page templates, workflows, data gate). Later tasks read `CLAUDE.md` first.

- [x] **Step 1: Create the folder skeleton**

```bash
mkdir -p wiki/sources wiki/entities wiki/concepts wiki/synthesis
touch wiki/sources/.gitkeep wiki/entities/.gitkeep wiki/concepts/.gitkeep wiki/synthesis/.gitkeep
```

- [x] **Step 2: Write `CLAUDE.md`** with exactly this content:

````markdown
# Meridian research wiki: schema

This repo holds an LLM-maintained research wiki that prepares the team for a one-on-one interview with Dana Okafor (VP of Operations, Meridian Markets). It follows the LLM wiki pattern (Karpathy). Spec: `docs/superpowers/specs/2026-09-20-meridian-research-wiki-design.md`.

## Layers

- `raw/`: immutable sources. Read them, never edit them.
- `wiki/`: yours. Create and maintain all pages here.
- `CLAUDE.md`: this file. Follow it.

## Data rules (non-negotiable)

Follow `data-handling-checklist.md`. Meridian's counsel prohibits customer records and employee data in any AI tool.

- Only **Allowed**-tier material may be in `raw/`: public research, the client brief, and team notes containing no client data.
- Never read, paste, summarize, or excerpt loyalty, labor, or POS data. If a file in `raw/` looks like any of these, stop and tell the user.
- Aggregates of loyalty or labor data are Restricted until Dana confirms in writing.

## Page conventions

Frontmatter on every wiki page except `index.md` and `log.md`:

```yaml
---
type: source | entity | concept | synthesis
sources: [page-name, ...]   # wiki/sources pages this page relies on
updated: YYYY-MM-DD
confidence: low | medium | high
---
```

- Link pages with `[[page-name]]` (file name without `.md`). File names are unique across all folders, lowercase, hyphenated.
- Every factual claim cites a source page, for example `([[client-brief]])`.
- Source pages also carry `tier: Allowed` and `raw_file: raw/<file>` in frontmatter.

## Workflows

### Ingest

1. **Data gate.** Confirm the source is public or the client brief, assign its tier, and put the tier in the source page and the log entry. If not Allowed, stop.
2. Read the source and tell the user the key takeaways.
3. Write `wiki/sources/<name>.md`: what it is, key points, and what it changes for the interview.
4. Create or update entity and concept pages. Add `[[links]]` both ways.
5. Update `wiki/synthesis/open-questions.md` and `wiki/synthesis/assumptions.md` where the source raises or resolves items.
6. Update `wiki/index.md`. Append to `wiki/log.md`.

### Query

Answer from the wiki with citations. If the answer is worth keeping, file it as a concept or synthesis page and log it.

### Lint

Report (and fix once the user approves): contradictions, stale claims, orphan pages (not in `index.md`), missing citations, knowledge gaps, and anything that looks like restricted client data. Log the pass.

## Interview guide

`wiki/synthesis/interview-guide.md` entries have: the question, why it matters (the decision it informs), supporting evidence (links), and priority (must ask | should ask | if time). Order so the must-asks fit a short session.
````

- [x] **Step 3: Write `wiki/index.md`**

```markdown
# Wiki index

Updated on every ingest and every new page.

## Sources
_none yet_

## Entities
_none yet_

## Concepts
_none yet_

## Synthesis
_none yet_
```

- [x] **Step 4: Write `wiki/log.md`**

```markdown
# Log

Append-only. Format: `## [YYYY-MM-DD] ingest | query | lint | <title>`

## [2026-09-20] setup | Wiki skeleton and CLAUDE.md schema created
```

- [x] **Step 5: Verify**

Run: `ls wiki wiki/synthesis && head -3 CLAUDE.md`
Expected: the four folders plus `index.md` and `log.md` are listed; `CLAUDE.md` starts with `# Meridian research wiki: schema`.

- [x] **Step 6: Propose the commit** (wait for your yes)

```bash
git add CLAUDE.md wiki raw data-handling-checklist.md docs
git commit -m "docs: add wiki skeleton, schema, checklist, spec, and plan"
```

**Done looks like:** the skeleton exists, `CLAUDE.md` states the layers, data rules, page conventions, and three workflows, and every earlier document is tracked in git.

**How you check it:** open `CLAUDE.md` and confirm the data rules match your checklist. Run `git status` and confirm it shows a clean tree after the commit. Confirm `raw/client-brief.md` is the only file in `raw/`.

---

### Task 2: Ingest the client brief

**Status:** Complete. Committed as `342cd73`.

**Files:**
- Create: `wiki/sources/client-brief.md`, `wiki/entities/meridian-markets.md`, `wiki/entities/dana-okafor.md`, `wiki/entities/marcus-it.md`, `wiki/entities/pasadena.md`, `wiki/synthesis/open-questions.md`, `wiki/synthesis/assumptions.md`
- Modify: `wiki/index.md`, `wiki/log.md`

**Interfaces:**
- Consumes: the ingest workflow and page conventions in `CLAUDE.md`; `raw/client-brief.md`.
- Produces: page names `client-brief`, `meridian-markets`, `dana-okafor`, `marcus-it`, `pasadena`, `open-questions`, `assumptions`, which later tasks link to.

- [x] **Step 1: Run the data gate.** Record in the source page: `tier: Allowed`, `raw_file: raw/client-brief.md`. Reason: the brief contains no customer or employee data.
- [x] **Step 2: Write `wiki/sources/client-brief.md`.** Summarize: company facts (14 stores, about $78M revenue, about 620 employees, LA/Orange/Ventura counties), the ask (dashboard of sales by store and category to decide the next location; Pasadena is the assumed site), the loyalty ambition (about 40,000 members, unused), available data (POS about three years, loyalty, labor, store attributes), the POS migration last spring, the AI-tool restriction, the eight-week timeline with a board preview in three weeks, and Dana's availability (travels Tuesdays and Wednesdays, slow to reply).
- [x] **Step 3: Write the four entity pages.** Each has frontmatter and cites `[[client-brief]]`. `meridian-markets`: profile and growth from 6 to 14 stores in 5 years via lease takeovers. `dana-okafor`: role, contact preferences, what she has asked for. `marcus-it`: IT contact who pulls the extract after the NDA. `pasadena`: the assumed next site, marked `confidence: low` because the brief gives no data on it.
- [x] **Step 4: Write `wiki/synthesis/open-questions.md`.** A ranked table with columns: rank, question, why it matters, source. Seed at least these from the brief: what "success" means beyond "increase revenue, reduce costs, improve experience"; why Pasadena is "obvious" and what alternatives were considered; what caused uneven store performance; how the POS migration affects data continuity across the three years; whether aggregates of loyalty data can be shared with the team's AI tools; what the board expects to see in three weeks; who decides the expansion; what a store's lease terms constrain.
- [x] **Step 5: Write `wiki/synthesis/assumptions.md`.** A table with columns: assumption, evidence, how to test (interview question or other), status. Seed: Pasadena is the frontrunner because of instinct, not data; "performance" means sales, not margin; the POS migration is a data break; prior openings followed lease availability rather than analysis.
- [x] **Step 6: Update `wiki/index.md` and `wiki/log.md`.** Add every new page with a one-line summary; log `## [2026-09-20] ingest | Client brief (tier: Allowed)`.
- [x] **Step 7: Verify**

```bash
for f in $(find wiki -name '*.md' ! -name index.md ! -name log.md); do n=$(basename "$f" .md); grep -q "\[\[$n\]\]" wiki/index.md || echo "NOT IN INDEX: $f"; done
grep -rEL "^type:" wiki/sources wiki/entities wiki/synthesis --include=*.md
```

Expected: no output from either command.

- [x] **Step 8: Propose the commit** (wait for your yes): `git add wiki && git commit -m "wiki: ingest client brief and seed entities and open questions"`

**Done looks like:** the brief is fully represented as a source page, four entity pages, a ranked open-questions page, and an assumptions page, all indexed and logged, with claims cited to `[[client-brief]]`.

**How you check it:** read `open-questions.md` and confirm the top three are the ones you'd want answered. Spot-check three claims in the entity pages against `raw/client-brief.md`. Confirm the verification commands print nothing.

---

### Task 3: You add the public sources

**Status:** Complete (revised approach). You supplied sources as URLs; Claude fetched each, saved an extract to `raw/`, and confirmed the tier before ingesting.

**Files:**
- Create (by you): `raw/<slug>.md` for each source you choose
- Modify: `wiki/log.md`

**Interfaces:**
- Produces: the source files that Task 4 ingests.

- [x] **Step 1: You add sources to `raw/`.** One markdown file per source, with the URL, publisher, and retrieval date at the top, plus the article text (if free to copy for personal study) or your own notes. Do not save paywalled text you don't have rights to; save the URL and your notes instead. Cover the three topics: store site selection, specialty grocery market, retail performance metrics.
- [x] **Step 2: You tell Claude which files are ready.** Claude lists what is in `raw/` and confirms each file is Allowed tier (public, or team notes with no client data) before reading it.
- [x] **Step 3: Log it.** Claude appends `## [date] query | Sources added by user` to `wiki/log.md`. (Done as one `ingest` log entry per source instead of a separate "Sources added" entry.)

**Done looks like:** `raw/` contains the client brief plus the sources you chose, each with its origin recorded, and Claude has confirmed the tier of each.

**How you check it:** run `ls raw` and confirm every file is one you added. Confirm each file names its URL or origin.

---

### Task 4: Ingest the public sources you added

**Status:** In progress. Three sources ingested: `icsc-new-grocery-formats`, `safegraph-site-selection-checklist`, `jll-grocery-report-2025`. More sources may follow, so steps stay open.

**Files:**
- Create: `wiki/sources/<slug>.md` (one per source you added)
- Modify: `wiki/index.md`, `wiki/log.md`

**Interfaces:**
- Consumes: files you added to `raw/`; the ingest workflow in `CLAUDE.md`.
- Produces: source page names that Task 5 cites.

- [ ] **Step 1: For each source you added, run the full ingest workflow** (data gate, takeaways to you, source page, index, log). Each source page ends with a "What this changes for the interview" section.
- [ ] **Step 2: After each source, discuss the takeaways with you** and pause if you want to redirect. Do not batch-ingest silently.
- [ ] **Step 3: Verify**

```bash
grep -L "^tier: Allowed" wiki/sources/*.md
grep -c "" wiki/log.md
```

Expected: the first command lists no files (every source page has its tier); the log has one ingest entry per source.

- [ ] **Step 4: Propose the commit** (wait for your yes): `git add raw wiki && git commit -m "wiki: ingest public sources"`

**Done looks like:** every source you added has a source page with a recorded tier, is listed in the index, and has a log entry.

**How you check it:** pick two source pages and compare their key points to the raw file. Run the verification commands.

---

### Task 5: Build concept pages and update synthesis

**Status:** Not started.

**Files:**
- Create: `wiki/concepts/site-selection.md`, `same-store-sales.md`, `sales-per-square-foot.md`, `category-mix.md`, `store-maturity.md`, `cannibalization.md`, `specialty-grocery-landscape.md`
- Modify: `wiki/synthesis/open-questions.md`, `wiki/synthesis/assumptions.md`, `wiki/index.md`, `wiki/log.md`, and entity pages where new facts apply

**Interfaces:**
- Consumes: source pages from Tasks 2 and 4.
- Produces: concept page names that the interview guide links to as evidence.

- [ ] **Step 1: Write each concept page.** Sections: definition, why it matters for Meridian's expansion decision, how it's measured, what the dashboard would need, and questions it raises for Dana. Every claim cites a source page. Where the sources don't cover something, say so instead of filling from memory, and set `confidence: low`.
- [ ] **Step 2: Cross-link.** Concept pages link to relevant entity pages (for example `[[pasadena]]`) and to each other.
- [ ] **Step 3: Update `open-questions.md` and `assumptions.md`** with new items the research raised, and re-rank.
- [ ] **Step 4: Update the index and log.**
- [ ] **Step 5: Verify**

```bash
for f in $(find wiki -name '*.md' ! -name index.md ! -name log.md); do n=$(basename "$f" .md); grep -q "\[\[$n\]\]" wiki/index.md || echo "NOT IN INDEX: $f"; done
grep -L "\[\[" wiki/concepts/*.md
```

Expected: no output from either (every page is indexed and every concept page cites at least one source).

- [ ] **Step 6: Propose the commit** (wait for your yes): `git add wiki && git commit -m "wiki: add concept pages and update synthesis"`

**Done looks like:** seven concept pages exist, each cited and cross-linked, and the open questions and assumptions reflect what the research added.

**How you check it:** read two concept pages and confirm you'd trust them in a conversation with Dana. Confirm any page marked `confidence: low` says what evidence is missing.

---

### Task 6: Write the interview guide

**Status:** Not started.

**Files:**
- Create: `wiki/synthesis/interview-guide.md`
- Modify: `wiki/index.md`, `wiki/log.md`

**Interfaces:**
- Consumes: `open-questions`, `assumptions`, concept and entity pages.
- Produces: the deliverable.

- [ ] **Step 1: Draft the guide.** Each entry: question phrased for Dana, why it matters (the decision it informs), supporting evidence (links to pages), priority (must ask | should ask | if time). Cover the seed themes from the spec: the Pasadena assumption, defining success, uneven store performance, loyalty data under the AI-tool restriction, POS migration continuity, and the three-week board timeline.
- [ ] **Step 2: Order it.** Must-asks first, sized so they fit a short session. Add a one-paragraph opening script explaining the team's data-handling stance so Dana can confirm the aggregate-sharing rule early.
- [ ] **Step 3: Show you the draft and revise** until you approve the must-ask list.
- [ ] **Step 4: Update the index and log.**
- [ ] **Step 5: Verify**

```bash
grep -c "must ask" wiki/synthesis/interview-guide.md
grep -c "\[\[" wiki/synthesis/interview-guide.md
```

Expected: at least 1 must-ask and at least as many `[[` links as questions.

- [ ] **Step 6: Propose the commit** (wait for your yes): `git add wiki && git commit -m "wiki: add interview guide"`

**Done looks like:** `interview-guide.md` has prioritized questions, each with rationale and linked evidence, and you've approved the must-ask list.

**How you check it:** read the must-asks and ask whether you could run the interview from them alone. Click through two evidence links and confirm they land on real pages.

---

### Task 7: Lint and definition-of-ready check

**Status:** Not started.

**Files:**
- Modify: any page with a lint finding; `wiki/log.md`

**Interfaces:**
- Consumes: the whole wiki.

- [ ] **Step 1: Run the lint pass** from `CLAUDE.md`: contradictions, stale claims, orphans, missing citations, knowledge gaps.
- [ ] **Step 2: Run the restricted-content scan**

```bash
grep -rEin "[a-z0-9._-]+@[a-z0-9-]+\.[a-z]{2,}|[0-9]{3}[-. ][0-9]{3}[-. ][0-9]{4}|member[_ ]?id|cashier|employee (name|id)" wiki raw
```

Expected: no matches, or only matches you review and confirm are harmless (for example an email address of a public source).

- [ ] **Step 3: Report findings to you** and fix the ones you approve.
- [ ] **Step 4: Check the spec's definition of ready** item by item and report pass or fail for each:
  - [ ] Brief and planned public sources ingested, each with a recorded tier
  - [ ] `open-questions.md` ranked
  - [ ] Every assumption has a linked interview question or another test
  - [ ] `interview-guide.md` has must-asks with cited evidence
  - [ ] Lint pass run and findings resolved
  - [ ] Nothing Restricted or Ask-first in `raw/` or `wiki/`
- [ ] **Step 5: Log the lint pass and propose the commit** (wait for your yes): `git add -A && git commit -m "wiki: lint pass and readiness check"`

**Done looks like:** lint findings are resolved or explicitly deferred by you, the scan is clean, and all six definition-of-ready items pass.

**How you check it:** read the readiness report, run the scan yourself, and skim `wiki/log.md` for the full history of operations.

---

## Self-review against the spec

| Spec section | Covered by |
|---|---|
| 2. Scope and constraints (sources, data rules, location) | Global Constraints; Tasks 1, 3, 4, 7 |
| 3. Architecture (layers, layout, conventions) | Task 1 (`CLAUDE.md`, skeleton); Tasks 2 and 5 (pages) |
| 4. Operations (ingest, query, lint) | `CLAUDE.md` in Task 1; used in Tasks 2, 4, 7 |
| 5. Interview guide | Task 6 |
| 6. Definition of ready | Task 7 |
| 7. Open items (source list) | Choices table; Task 3 (you supply the sources) |
