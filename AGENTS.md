# EGA ATLAS

EGA ATLAS is the knowledge base for everything EGA builds and runs: Gymtrack, Helm, EGA Skills, the job hunt, and whatever comes next. It is plain Markdown in git, read by a human in Obsidian and written by agents.

Think of it as the memory every agent session starts from, so no one has to re-explain a project twice.

Based on Karpathy's LLM Wiki pattern: the agent writes and maintains the wiki; the human reads it and asks questions.

## What we never compromise on

1. **Current, not historical.** A page says what is true now. History lives in `log.md` and git. If a page is stale, it is a bug.
2. **Small and obvious.** Plain Markdown, a few folders, no tooling to run. If a structure needs explaining, it is too clever.
3. **One source of truth.** One page per subject. Linear holds tickets, GitHub holds code, the atlas holds state, decisions and the why. Link to them; never copy them.
4. **Read before you act.** Every session starts from the atlas. Every session that changes something ends by updating it.

## A note from Abdelilah

I build a lot and I start new things too easily. Help me finish. Right now the order is: a full-time job first, Gymtrack second, then Helm and EGA Skills. Everything else is parked unless I say otherwise.

I like simple systems that feel obvious. Don't add pages, folders or process because they look organized. Write the smallest thing that keeps the next session from being lost. Fight scope creep, in the atlas and in the projects it describes.

Tell me plainly when something is stuck, drifting, or contradicts what I decided.

These instructions are good defaults, not hard rules. What I say in the session wins.

## Glossary

- **you** — the agent reading this file.
- **I / me** — Abdelilah, the owner of the atlas.
- **the team** — Abdelilah and his two Gymtrack co-founders.
- **agent** — any AI working on EGA (T3 Code with Codex or Claude, Hark, ChatGPT).
- **page** — one Markdown file in `wiki/` about one subject.
- **source** — an original document in `raw/` (PRD, notes, transcript, export). Never edited.
- **project** — something being built or pursued: Gymtrack, Helm, EGA Skills, Job hunt.
- **parked** — a project deliberately on hold. It keeps its page; it gets no new work.

## Layout

- `index.md` — catalog of every page with a one-line summary. Read it first.
- `log.md` — append-only timeline: `YYYY-MM-DD | agent | page | what changed`.
- `wiki/` — the pages.
- `raw/` — sources, by topic: `raw/<topic>/<yyyy-mm-dd>-<name>.md`.
- `.agents/skills/` — workflows for this repo.

## Skills

| Skill | Use when |
|---|---|
| `atlas-query` | Answering a question, or starting work on a project |
| `atlas-update` | Finishing a task that changed state, a decision, or next steps |
| `atlas-ingest` | A new source lands in `raw/`, or "add this to the atlas" |
| `atlas-lint` | Weekly (Sunday planning), or when asked for a health check |

## The three ways to hurt the atlas

1. **Duplicating.** Creating `gymtrack-v2.md` or `helm-notes.md` next to an existing page. Search `index.md` and `wiki/` first; edit the page that exists.
2. **Appending history.** Adding "Update 10/20: …" lines to the bottom of a page. Rewrite the section so it reads as the current state; the old version is in git.
3. **Leaking.** This repo may be public. Never write secrets, tokens, passwords, keys, IDs, phone numbers, addresses, salaries, or recruiter contacts. Link to where they live instead.

## Touch every place

The most common defect is updating one page and leaving the rest wrong. Before calling an update done, check which of these applied:

- **The page itself.** Current state, Decisions (with the why), Open questions, Next steps.
- **index.md.** New page added? Summary still accurate?
- **log.md.** One line for what changed.
- **Linked pages.** A decision in one project often changes another (Helm tracks Gymtrack; the job hunt changes how much time projects get). Update both or note the open question.
- **Priorities.** If the change affects what comes first, say so on the page and to me.
- **Reverse states.** Parked something? Write what would un-park it. Opened a question? Close it when answered.

## Writing pages

- File names: `lowercase-with-hyphens.md`.
- Sections: one-line summary, Current state, Decisions, Open questions, Next steps, Links.
- Short sentences. Facts over adjectives. Dates as `YYYY-MM-DD`.
- Mark guesses as `(inferred)`. Cite sources: `(source: raw/<path>)`.
- Link pages relatively: `[Helm](wiki/helm.md)`. Works on GitHub and in Obsidian.
- Next steps are concrete and small enough to start today. "Ship GYM-91" not "improve permissions".
- English for pages. French or Darija sources are fine in `raw/`; summarize them in English.

## Commits

- One logical change per commit: `atlas: <page> — <change>`, e.g. `atlas: gymtrack — Sprint 2 RBAC shipped`.
- Pull before you write. Two agents may be working at once; never force-push.
- Don't commit scratch notes, plans for a single session, or agent output dumps. Summarize them into a page or leave them out.

## Taste

- A page you can read in one minute beats a complete one.
- If reading the code or the Linear ticket answers the question, link it instead of writing it.
- Delete what is no longer true. Removing a stale line is a good edit.
- If a rule here fights the task in front of you, say so and ask me before breaking it.
