# EGA ATLAS — Agent Instructions

EGA ATLAS is the knowledge base for all EGA work (Gymtrack, Helm, EGA Skills, job hunt, and more).
The agent writes and maintains the wiki; the human reads it in Obsidian and asks questions.
Based on Karpathy's LLM Wiki pattern.

## Layout
- `index.md` — catalog of every wiki page with a one-line summary. Read it first.
- `log.md` — append-only timeline of changes.
- `wiki/` — compiled pages, one per subject (projects, decisions, tools, people).
- `raw/` — immutable source material (PRDs, notes, transcripts, exports). Read, never edit.
- `.agents/skills/` — workflows for this repo. Use the one that matches the task.

## Skills
| Skill | Use when |
|---|---|
| `atlas-query` | Answering a question or starting work on a project: read before you act |
| `atlas-update` | Finishing any task that changed a project's state, decisions or next steps |
| `atlas-ingest` | A new file lands in `raw/` or the user says "add this to the atlas" |
| `atlas-lint` | Weekly, or when asked to health-check the atlas |

## Always
1. Start: read `index.md`, then the relevant page(s) in `wiki/`.
2. Finish: update the page(s), `index.md` if a page was added, and append to `log.md`:
   `YYYY-MM-DD | agent | page | what changed`
3. Commit with a short message: `atlas: <page> — <change>`.

## Page rules
- One page per subject; edit, never duplicate. File names: lowercase-with-hyphens.md.
- Write the current state, not history. History lives in `log.md` and git.
- Every page has: summary line, Current state, Decisions (with the why), Open questions, Next steps, Links.
- Link pages with relative markdown links, e.g. `[Helm](wiki/helm.md)` (works on GitHub and in Obsidian).
- Mark guesses as `(inferred)`. Cite the raw file a fact came from: `(source: raw/<file>)`.
- Never store secrets, tokens, passwords, or personal IDs.
