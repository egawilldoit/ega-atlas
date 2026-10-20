---
name: atlas-ingest
description: Use when a file lands in raw/inbox/ (PRD, notes, transcript, export, article) or the user says "add this to the atlas". Compiles the source into wiki pages without changing the original.
---

# Atlas Ingest

1. `git pull`.
2. Move the source from `raw/inbox/` to `raw/<topic>/YYYY-MM-DD-<short-name>.<ext>` (topic = project or area name). Never edit its content.
3. Scan it for secrets, contacts, addresses or salaries. If present, stop and ask before committing it; the repo may be public.
4. Read it fully. List the subjects it touches (projects, areas, concepts, decisions).
5. For each subject, follow `atlas-update`: extend the existing page, or create one from `templates/`. Add facts with `(source: raw/<path>)`.
6. Contradiction with an existing page? Don't overwrite silently. Put both versions under Open questions with sources, and tell the user.
7. Update `index.md` for new pages and `now.md` if priorities changed.
8. Append to `log.md`:
   ```
   ## [YYYY-MM-DD] ingest | raw/<path> | <agent>
   Pages touched: ...
   ```
9. Commit `atlas: ingest <short-name>` and push.
