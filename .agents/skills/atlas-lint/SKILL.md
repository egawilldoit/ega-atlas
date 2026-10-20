---
name: atlas-lint
description: Use weekly or when asked to health-check EGA ATLAS. Finds broken links, orphan pages, stale or contradictory facts, and index gaps, then fixes the safe ones.
---

# Atlas Lint

Check, then report:
1. **Index** — every file in `wiki/` is listed in `index.md`, and every listed page exists.
2. **Links** — relative links resolve; fix broken ones.
3. **Orphans** — pages nothing links to; link them from a related page or index.
4. **Staleness** — pages not updated in 14+ days (check `git log`) with open Next steps; flag them.
5. **Contradictions** — the same fact stated differently across pages; list them, don't guess.
6. **Template** — pages missing a required section; add the empty section.
7. **Secrets** — anything that looks like a token, password or key; remove it and warn the user.

Fix only mechanical issues (1, 2, 3, 6, 7). Report the rest as a short list.
Append to `log.md`: `YYYY-MM-DD | agent | lint | summary`. Commit: `atlas: lint`.
