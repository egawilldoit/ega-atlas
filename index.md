# EGA ATLAS - Index

Start with [now.md](now.md). Every wiki page is listed here once.

## Projects

| Page | Status | Summary |
|---|---|---|
| [Gymtrack](wiki/projects/gymtrack.md) | active | Sprint 2 accepted; full 35-route frontend redesign and persistent sidebar merged to develop@d7882f5 with protected CI green; hosted acceptance and Sprint 3 security/deletion pending |
| [Helm](wiki/projects/helm.md) | active | Native T3 control plane; v0.2 DEV frontend deployed, Production v0.1 patched, acceptance open |
| [EGA Skills](wiki/projects/ega-skills.md) | active | CI Batches 0–5 shipped (#197/#198), ~48% faster PR and ~54% faster full CI vs historical; live-release acceptance tracked separately |
| [EGA House](wiki/projects/ega-house.md) | active | CI PR #341 merged; exact-SHA main full CI green in 190s; app deployment/E2E/CPU tracked separately |

## Areas

| Page | Status | Summary |
|---|---|---|
| [Job hunt](wiki/areas/job-hunt.md) | active | DevOps/Cloud, then CRM; Rabat/Casablanca, five applications/day target |
| [Engineering portfolio](wiki/areas/engineering-portfolio.md) | active | Existing secondary codebases; inventory only, not an expanded priority queue |

## Concepts

| Page | Summary |
|---|---|
| [Agent delivery workflow](wiki/concepts/agent-delivery-workflow.md) | Shared rules for coding agents, evidence, approvals, VM work and knowledge capture |
| [Agent runtime VM](wiki/concepts/agent-runtime-vm.md) | Shared Ubuntu execution environment, resource/credential hygiene and worktree safety |
| [Time management](wiki/concepts/time-management.md) | Visual, transcript-audited guide to priorities, real costs, choices, efficiency and planning |

## Decisions

| Page | Status | Summary |
|---|---|---|
| [0001 Atlas structure](wiki/decisions/0001-atlas-structure.md) | accepted | LLM Wiki layers + PARA-style wiki + now.md |
| [0002 Native T3 execution](wiki/decisions/0002-native-t3-execution-ownership.md) | accepted | Helm coordinates T3; no second executor or auto-merge |
| [0003 Shared DEV migrations](wiki/decisions/0003-gymtrack-shared-dev-migrations.md) | accepted | Serialized, auditable shared Supabase DEV SQL delivery |
| [0004 Sprint 2 acceptance](wiki/decisions/0004-gymtrack-sprint-2-acceptance.md) | accepted | Close delivered scope; preserve verification and deletion debt in Sprint 3 |
| [0005 EGA Skills automatic releases](wiki/decisions/0005-ega-skills-automatic-production-release.md) | accepted | Main code/catalog ship through Vercel Git + CI gate, live verification and guarded recovery |

## Archive

| Page | Summary |
|---|---|
| _none yet_ | |
