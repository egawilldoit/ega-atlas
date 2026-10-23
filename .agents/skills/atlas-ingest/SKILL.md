---
name: atlas-ingest
description: Use when a file lands in raw/inbox/ (PRD, notes, transcript, export, article) or the user says "add this to the atlas". Compiles the source into wiki pages without changing the original.
---

# Atlas Ingest

1. `git pull`.
2. **Safety check BEFORE moving or staging the source.** This is a public repository. Inspect the original locally for secrets, personal data, contacts, salaries, private chat transcripts, customer records, unredacted logs, employer/client confidential or unpublished technical content, and uncertain sharing rights. If any are present or classification is unclear, **do not add the source to `raw/` or commit it**. Keep the original only in its authorized private location; ask for clearance or record a short nonconfidential summary without including private content or identifiers.
3. Only if the original is approved for public sharing: move it from `raw/inbox/` to `raw/<topic>/YYYY-MM-DD-<short-name>.<ext>` (topic = project or area name). Never modify the approved original.
4. Read only public-safe material for ingestion. List the subjects it touches (projects, areas, concepts, decisions).
5. For each subject, follow `atlas-update`: extend the existing page, or create one from `templates/`. Cite `(source: raw/<path>)` **only when that approved public-safe file actually exists in this repo**; never link to an uncommitted confidential original.
6. Contradiction with an existing page? Don't overwrite silently. Put both versions under Open questions with sources, and tell the user.
7. Update `index.md` for new pages and `now.md` if priorities changed.
8. Append to `log.md`:
   ```
   ## [YYYY-MM-DD] ingest | raw/<path> | <agent>
   Pages touched: ...
   ```
9. Commit `atlas: ingest <short-name>` and push.
