# Log

Append-only. Newest at the bottom. Format:

```
## [YYYY-MM-DD] <op> | <page or source> | <agent>
What changed and why.
```

Ops: create, update, ingest, decision, archive, lint, schema.

## [2026-10-20] create | all | Hark
Created EGA ATLAS: AGENTS.md, index, log, 4 pages, 4 skills (query, update, ingest, lint).

## [2026-10-20] schema | AGENTS.md | Hark
Rewrote rules: principles, note from Abdelilah, glossary, failure modes, touch-every-place checklist.

## [2026-10-20] schema | structure | Hark
Restructured: now.md, wiki/{projects,areas,concepts,decisions,archive}, raw/{inbox,assets}, templates/, frontmatter on every page, grep-able log. See wiki/decisions/0001-atlas-structure.md.

## [2026-10-23] update | now.md, project and area pages | ChatGPT
Backfilled verified October 23 project status, role/DEV blockers, priorities and career context from Atlas, current GitHub/GitLab/Linear and accessible chat context. Marked DEV vs product acceptance distinctly; bulk 1,000-chat coverage not claimed.

## [2026-10-23] create | wiki/projects/ega-house.md, wiki/concepts/agent-delivery-workflow.md | ChatGPT
Preserved EGA House as parked and distilled durable cross-project agent defaults without duplicating tickets or secret-bearing source material.

## [2026-10-23] schema | AGENTS.md, index.md | ChatGPT
Linked the reusable delivery workflow into the Atlas contract and index; no folder or template layout changes.

## [2026-10-23] lint | all | ChatGPT
Verified 7 wiki pages against index, required frontmatter, template sections, relative links and related references; now.md is 212 words. No mechanical issues found. Full source freshness and 1,000-chat coverage were not certified.

## [2026-10-23] update | wiki/projects/helm.md, now.md | ChatGPT
Pass 1/5: verified open draft PRs #35–#37 and separated proposed write-safety/issue UI/docs from merged develop or hosted acceptance; revised current next steps.

## [2026-10-23] update | wiki/projects/gymtrack.md | ChatGPT
Pass 2/5: verified draft MRs !111/!112 and the GYM-75 Done-label versus unapproved deletion-policy discrepancy. Kept irreversible deletion blocked pending explicit signoff, not inferred approval.

## [2026-10-23] create | wiki/concepts/agent-runtime-vm.md | ChatGPT
Pass 3/5: preserved reusable verified VM workflow: gated heavy commands, dated toolchain facts, worktree/disk hygiene and ephemeral auth limits; linked it from the concept index and agent workflow. No secrets or precise host identifiers recorded.

## [2026-10-23] update | wiki/projects/ega-skills.md, now.md | ChatGPT
Pass 4/5: distinguished the published v2.0.0 GitHub Release from current 2.0.1 package and stale README “candidate” wording. Kept browser authorization and promoted-artifact acceptance unresolved.
