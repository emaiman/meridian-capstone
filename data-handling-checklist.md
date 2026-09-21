# Data handling checklist: Meridian Markets

Source: `raw/client-brief.md` (Terms of engagement). Internal use, workshop team only.

## Data tiers

| Tier | Data | AI tools (Claude, ChatGPT, Copilot, etc.) |
|---|---|---|
| **Allowed** | Sales totals by store and week; store attributes (square footage, opening date, lease terms) | OK |
| **Restricted** | Loyalty program membership and purchase history; labor scheduling and hours; any excerpt of either; any aggregate derived from either (until Dana confirms in writing) | **Never** |
| **Ask first** | POS transactions (line-level, may carry loyalty or cashier IDs); anything ambiguous or not listed above | Not until confirmed with Dana or Marcus |

Default rule: if you're unsure of the tier, treat it as Restricted.

## 1. Intake and storage

- [ ] NDA is signed before any data is requested or received
- [ ] Request the extract through Marcus (Meridian IT)
- [ ] Every file received is assigned a tier (Allowed / Restricted / Ask first) and the tier is recorded
- [ ] POS extract is checked for loyalty IDs, cashier IDs, or other personal fields; if present, it is Restricted until Dana or Marcus says otherwise
- [ ] Restricted files are kept out of the git repo (gitignored or stored outside it) and out of any cloud sync that feeds an AI tool
- [ ] **Open item:** where restricted files may be stored and who may access them. Decide after the NDA and Marcus specify what's allowed, then update this section

## 2. Analysis and AI use

**Gate: before pasting, uploading, or querying anything in an AI tool**

- [ ] What tier is this data? (If unsure, stop and treat as Restricted)
- [ ] Is it Allowed? If not, do not use an AI tool with it
- [ ] Is it an excerpt, sample, screenshot, or summary of Restricted data? If yes, it's Restricted
- [ ] Are file names, column values, or error messages included in the prompt free of customer or employee details?

**Working with Restricted data**

- [ ] Analyze locally (SQL, Python, Excel) on your own machine, with no AI assistance touching the data
- [ ] Aggregates from loyalty or labor data stay Restricted until Dana confirms in writing (email) that the specific aggregate is fine
- [ ] Save Dana's written confirmation, then record the aggregate as Allowed
- [ ] Only the confirmed aggregate goes into an AI tool, not the underlying records
