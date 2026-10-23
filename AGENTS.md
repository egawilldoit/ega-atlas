# EGA ATLAS

EGA ATLAS is the knowledge base for everything EGA builds and runs: Gymtrack, Helm, EGA Skills, the job hunt, and whatever comes next. It is plain Markdown in git, read by a human in Obsidian and written by agents.

It is the memory every agent session starts from, so no one has to explain a project twice.

Based on Karpathy's LLM Wiki pattern: agents write and maintain the wiki; the human reads it, adds sources, and asks questions.

## What we never compromise on

1. **Current, not historical.** A page says what is true now. History lives in `log.md` and git. A stale page is a bug.
2. **Small and obvious.** Plain Markdown, a few folders, no tooling to run. If a structure needs explaining, it is too clever.
3. **One source of truth.** One page per subject. Linear holds tickets, GitHub holds code, the atlas holds state, decisions and the why. Link to them; never copy them.
4. **Read before you act, write before you leave.** Every session starts from `now.md`. Every session that changes something ends by updating the atlas.

## A note from Abdelilah

I build a lot and I start new things too easily. Help me finish. The current order of priorities lives in `now.md`; respect it and tell me when a task fights it.

I like simple systems that feel obvious. Don't add pages, folders or process because they look organized. Write the smallest thing that keeps the next session from being lost. Fight scope creep, in the atlas and in the projects it describes.

Tell me plainly when something is stuck, drifting, or contradicts what I decided.

These instructions are good defaults, not hard rules. What I say in the session wins.

## Working defaults (reviewed 2026-10-23)

Use [Agent delivery workflow](wiki/concepts/agent-delivery-workflow.md) for cross-project operating defaults. In brief:

- Get fresh repo/issue state before coding. Ship one bounded outcome on an isolated branch/worktree; avoid scope creep and needless tools.
- No subagents by default. A request can opt in per execution with its chosen model.
- Separate code, review, merge, DEV delivery, actual browser/runtime acceptance and Production acceptance. Report precise evidence; an agent saying "done" is not proof.
- Prepare PRs/MRs without automatic merge or external production/resource changes, unless explicitly authorized for that project and operation.
- Preserve secrets, existing architecture and the current DB migration protocol. On the Ubuntu VM, use its `heavy` guard for competing costly commands when installed.
- Treat old chat recollections as leads; current Linear, Git and runtime evidence control the state. Historical chat retrieval is not exhaustive.

## Glossary

- **you**: the agent reading this file.
- **I / me**: Abdelilah, the owner of the atlas.
- **agent**: any AI working on EGA (T3 Code with Codex or Claude, Hark, ChatGPT).
- **page**: one Markdown file in `wiki/` about one subject.
- **source**: an original document in `raw/` (PRD, notes, transcript, export, article). Never edited.
- **project**: has a finish line (Gymtrack, Helm, EGA Skills). Lives in `wiki/projects/`.
- **area**: ongoing, no finish line (job hunt, X growth, learning). Lives in `wiki/areas/`.
- **concept**: a tool, technology or pattern reused across projects (T3 Code, Supabase auth, RBAC). Lives in `wiki/concepts/`.
- **decision**: one important choice, with options and the why. Lives in `wiki/decisions/`.
- **parked**: deliberately on hold. Keeps its page with `status: parked`; gets no new work.

## Layout

```
AGENTS.md          these rules
now.md             current priorities and active work. Read FIRST, every session
index.md           every page, one line each, grouped by folder
log.md             append-only timeline
wiki/projects/     things with a finish line
wiki/areas/        ongoing responsibilities
wiki/concepts/     reusable tech and patterns
wiki/decisions/    NNNN-short-title.md, one per decision
wiki/archive/      finished or abandoned pages (moved here, never deleted)
raw/inbox/         new sources waiting to be ingested
raw/<topic>/       ingested sources: raw/<topic>/YYYY-MM-DD-name.md
raw/assets/        images and attachments
templates/         skeletons for each page type. Copy, never edit in place
.agents/skills/    workflows for this repo
```

Do not create new top-level folders or new `wiki/` subfolders. If something does not fit, ask me.

## Where does it go?

| It is... | Put it in |
|---|---|
| Something being built with an end state | `wiki/projects/` |
| An ongoing responsibility | `wiki/areas/` |
| A tool, library, service or pattern used in 2+ places | `wiki/concepts/` |
| A choice that would be costly to reverse, or that I will forget the reason for | `wiki/decisions/` |
| A document someone wrote (PRD, notes, transcript, article) | `raw/`, then ingest |
| Finished, abandoned or parked for good | move to `wiki/archive/`, keep the frontmatter |
| A ticket, a bug, a task | Linear. Link it, don't copy it |
| A secret, password, contact, address or salary | Nowhere in this repo |

When unsure between two folders, pick the one that already has the closest page and link from the other.

## Every session

**Start**
1. Read `now.md`.
2. Read `index.md` and open only the pages your task needs. Follow links one level deep.
3. `git pull` before you write anything.

**During**
- If a fix or approach fails twice, search the atlas (`grep -ri` in `wiki/` and `raw/`) before trying a third. A lesson may already be written.
- Do not edit pages while only answering a question, unless I ask.

**End** (when you changed state, a decision, or next steps)
1. Update the page(s) with the `atlas-update` skill.
2. Run the "Touch every place" checklist below.
3. Append to `log.md`, commit, push.

## Skills

| Skill | Use when |
|---|---|
| `atlas-query` | Answering a question, or starting work on a project or area |
| `atlas-update` | Finishing a task that changed state, a decision, or next steps |
| `atlas-ingest` | A file lands in `raw/inbox/`, or I say "add this to the atlas" |
| `atlas-lint` | Weekly (Sunday planning), or when I ask for a health check |

## Page rules

Every page starts with frontmatter. Required keys:

```yaml
---
type: project | area | concept | decision
status: active | parked | done | archived        # decisions: proposed | accepted | superseded
updated: YYYY-MM-DD                               # change it on every edit
related: [gymtrack, helm]                         # file names without .md
---
```

Then the sections from its template in `templates/`. Keep every section, even if empty (`-`).

- File names: `lowercase-with-hyphens.md`. Decisions: `NNNN-short-title.md`, next free number.
- Links are relative to the current file: from `wiki/projects/gymtrack.md`, Helm is `[Helm](helm.md)` and a concept is `[RBAC](../concepts/rbac.md)`. They work on GitHub and in Obsidian.
- Short sentences. Facts over adjectives. Dates as `YYYY-MM-DD`.
- Mark guesses `(inferred)`. Cite sources: `(source: raw/<path>)`.
- Next steps are concrete and small enough to start today: "Ship GYM-91", not "improve permissions".
- English for pages. French or Darija sources are fine in `raw/`; summarize them in English.
- A page you can read in one minute beats a complete one. If the code or the Linear ticket answers it, link instead of writing.

## The four ways to hurt the atlas

1. **Duplicating.** Creating `gymtrack-v2.md` or `helm-notes.md` next to an existing page. Search `index.md` and `wiki/` first; extend the page that exists.
2. **Appending history.** Adding "Update 10/20: ..." to the bottom of a page. Rewrite the section so it reads as the current state; git keeps the old version.
3. **Silently overwriting.** A new source or session contradicts a page. Don't just replace the fact: if the newer one is clearly right, update it and say why in `log.md`; if unsure, put both under Open questions with their sources.
4. **Leaking.** This repo may be public. Never write secrets, tokens, passwords, keys, account IDs, phone numbers, emails, addresses, salaries or recruiter contacts. Write "stored in <tool>" instead.

## Touch every place

The most common defect is updating one page and leaving the rest wrong. Before calling an update done, check:

- **The page.** Frontmatter `updated` and `status`; Current state, Decisions, Open questions, Next steps.
- **now.md.** Did priorities or active work change? Keep it under ~300 words.
- **index.md.** New, moved or renamed page? Summary still true?
- **log.md.** One entry for what changed.
- **Related pages.** A change in one project often affects another (Helm tracks Gymtrack; the job hunt changes how much time projects get). Update both, or add an open question on the other.
- **Decisions.** Made a lasting choice? Create a decision page and link it from the project. Reversed one? Set the old one to `superseded` and link the new one.
- **Reverse states.** Parked something? Write what would un-park it. Opened a question? Close it when answered. Moved to archive? Fix links that pointed to it.

## log.md format

Newest at the bottom. One entry per change, always this shape so it can be grepped:

```
## [YYYY-MM-DD] <op> | <page or source> | <agent>
One or two lines: what changed and why.
```

`<op>` is one of: `create`, `update`, `ingest`, `decision`, `archive`, `lint`, `schema` (changes to AGENTS.md, skills, templates).
Last five entries: `grep "^## \[" log.md | tail -5`.

## Commits

- One logical change per commit: `atlas: <page> - <change>`, e.g. `atlas: gymtrack - Sprint 2 RBAC shipped`.
- Pull before you write. Several agents may work at once; never force-push, never rewrite history.
- If a push is rejected, pull, re-read the pages you touched, merge by hand, then push. Never discard someone else's edit.
- Don't commit scratch notes, single-session plans or agent output dumps. Summarize them into a page or leave them out.

## Obsidian

- Settings > Files and links: new link format "Relative path to file", use `[[Wikilinks]]` off, attachment folder `raw/assets`.
- Useful plugins: Dataview (tables from frontmatter), Obsidian Git (auto pull/push), Web Clipper (save articles to `raw/inbox/`).

## When a rule fights the task

Say so and ask me before breaking it. If a rule here turns out wrong, propose the change; edits to this file are logged as `schema`.
