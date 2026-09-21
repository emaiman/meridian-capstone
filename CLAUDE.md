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
