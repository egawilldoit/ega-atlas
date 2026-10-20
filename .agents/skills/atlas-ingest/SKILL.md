---
name: atlas-ingest
description: Use when a new source lands in raw/ (PRD, notes, transcript, export, article) or the user says "add this to the atlas". Compiles the source into wiki pages without touching the original.
---

# Atlas Ingest

1. Put the source in `raw/<topic>/<yyyy-mm-dd>-<short-name>.md` (or keep its file). Never edit it afterwards.
2. Read it fully. List the subjects it touches (projects, decisions, tools, people).
3. For each subject: update the existing page in `wiki/`, or create one if none exists.
   Add facts with `(source: raw/<path>)`. Note contradictions with existing pages under Open questions; don't silently overwrite.
4. Add any new pages to `index.md`.
5. Append to `log.md`: `YYYY-MM-DD | agent | ingest raw/<path> | pages touched`.
6. Commit: `atlas: ingest <short-name>`.
