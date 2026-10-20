---
type: decision
status: accepted
updated: 2026-10-20
related: []
---

# 0001 Atlas structure

How EGA ATLAS is organized.

## Context
Agents kept re-learning projects every session. Knowledge was scattered across chats, Linear and repos.

## Options
1. Flat `wiki/` folder (first version). Simple, but mixes projects, ongoing areas and reusable tech.
2. Full PARA vault. Familiar, but built for humans filing notes, not agents maintaining a wiki.
3. Karpathy LLM Wiki layers (raw, wiki, schema) with PARA-style folders inside `wiki/`, plus `now.md`.

## Decision
Option 3. `now.md` gives every session the current priorities in one read. Folders say what kind of page it is, frontmatter makes pages queryable in Obsidian Dataview, and decisions get their own pages so the why survives.

## Consequences
- Agents must keep `now.md`, `index.md` and frontmatter current; `atlas-lint` checks it.
- No new folders without asking.

## Links
- Karpathy LLM Wiki: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
