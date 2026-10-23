---
name: atlas-lint
description: Use weekly or when asked to health-check EGA ATLAS. Finds broken links, orphans, missing frontmatter, stale or contradictory facts and index gaps, fixes the safe ones and reports the rest.
---

# Atlas Lint

1. `git pull`.
2. Check:
   1. **Index**: every file in `wiki/` (except `.gitkeep`) is in `index.md` under the right section, with the right status; every listed page exists.
   2. **Frontmatter**: every page has `type`, `status`, `updated`, `related`, and `type` matches its folder.
   3. **Template**: every page has its template's sections.
   4. **Links**: relative links resolve from the file's own folder; `related` names point to real files. When checking Markdown links, ignore link-looking text inside fenced code blocks or inline backticks (examples are not active links).
   5. **Orphans**: pages nothing links to; link them from a related page.
   6. **now.md**: under ~300 words, `updated` within 7 days, priorities match the pages' `status`.
   7. **Staleness**: active pages with `updated` older than 14 days and open Next steps.
   8. **Duplicates**: pages about the same subject; propose a merge.
   9. **Contradictions**: the same fact stated differently across pages.
   10. **Secrets**: anything like a token, password, key, email, phone, address or salary.
   11. **Inbox**: files still waiting in `raw/inbox/`.
3. Fix only mechanical issues (1-5, and 10 by removing the text). Report the rest as a short list to the user.
4. Append to `log.md`:
   ```
   ## [YYYY-MM-DD] lint | all | <agent>
   Fixed: ... Reported: ...
   ```
5. Commit `atlas: lint` and push.
