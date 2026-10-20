---
name: atlas-update
description: Use at the end of any task that changed a project's state, a decision, or the next steps. Keeps EGA ATLAS current so the next session starts where this one ended.
---

# Atlas Update

1. Open the project page in `wiki/` (create it from the template below if missing, and add it to `index.md`).
2. Rewrite, don't append:
   - **Current state** — what exists now (shipped, in progress, blocked).
   - **Decisions** — what was decided and why. Replace decisions that changed.
   - **Open questions** — remove answered ones.
   - **Next steps** — the next 1–5 concrete actions.
3. Append one line to `log.md`: `YYYY-MM-DD | agent | page | what changed`.
4. Commit: `atlas: <page> — <change>`.

## Page template
```
# <Name>

<one-line summary>

## Current state
## Decisions
## Open questions
## Next steps
## Links
```
