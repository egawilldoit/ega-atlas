---
name: atlas-update
description: Use at the end of any task that changed a project's state, a decision, or the next steps. Keeps EGA ATLAS current so the next session starts where this one ended.
---

# Atlas Update

1. `git pull`.
2. Find the page: check `index.md`, then `grep -ril "<name>" wiki/`. Edit the existing page; never create a near-duplicate.
3. No page yet? Copy the right file from `templates/` into the right `wiki/` folder (see "Where does it go?" in AGENTS.md) and fill the frontmatter.
4. Rewrite, don't append:
   - **Frontmatter**: set `updated` to today; fix `status` and `related`.
   - **Current state**: what exists now (shipped, in progress, blocked).
   - **Decisions**: one line each with the why. A lasting or costly choice also gets a page in `wiki/decisions/` (next free NNNN), linked here.
   - **Open questions**: remove answered ones; add contradictions with their sources.
   - **Next steps**: the next 1-5 concrete actions.
5. Run "Touch every place" from AGENTS.md: `now.md`, `index.md`, related pages, decisions, reverse states.
6. Moving a page to `wiki/archive/`: set `status: archived`, move it, update `index.md`, fix every link to it (`grep -rl "<file>.md" .`).
7. Append to `log.md`:
   ```
   ## [YYYY-MM-DD] update | <page> | <agent>
   What changed and why.
   ```
8. Commit `atlas: <page> - <change>` and push. Rejected? Pull, merge by hand, push. Never force-push.
