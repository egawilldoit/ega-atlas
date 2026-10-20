---
name: atlas-query
description: Use when answering a question about EGA work or before starting a task on a project or area. Reads EGA ATLAS (now.md, index.md, then pages) so you act on the current state instead of rediscovering it.
---

# Atlas Query

1. Read `now.md`, then `index.md`. Pick the pages that match the question or task.
2. Read those pages, then follow `related` and links one level deep if needed.
3. Not in the index? `grep -ri "<term>" wiki/ raw/` before saying it isn't there.
4. Answer from the pages, citing them: `(wiki/projects/gymtrack.md)`. If the atlas doesn't cover it, say so plainly; don't fill gaps from memory.
5. If a page contradicts what you see in the code or Linear, trust the code/Linear for facts and flag the stale page.
6. If the answer is reusable (a decision, comparison, how-to, lesson), offer to save it with `atlas-update`.
7. Don't edit pages during a query unless asked.
