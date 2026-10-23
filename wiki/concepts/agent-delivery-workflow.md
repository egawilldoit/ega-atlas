---
type: concept
status: active
updated: 2026-10-23
related: [gymtrack, helm, ega-skills, ega-house, job-hunt]
---

# Agent delivery workflow

Shared operating defaults reconstructed from accessible conversations and current EGA work; **not** a complete audit of the last 1,000 chats. Project `AGENTS.md`, live issue requirements and explicit owner instructions override these defaults.

## How we use it

- **Start with reality.** Read the repo's `AGENTS.md`, relevant architecture, current integration branch and current Linear issue. Fetch/pull before work. For Gymtrack, current integration is `develop`; never assume default `main` equals current delivery.
- **Ship one outcome.** Prefer one bounded issue and one isolated branch/worktree; analyze, implement, independently review, verify behavior, then open a PR/MR. Parallel work only if owners/files/migrations do not conflict.
- **Subagents are OFF by default.** Enable only when requested for that execution, with explicit role/model. A model's report is input, not independent proof.
- **Authority boundaries.** **Atlas** holds compact project memory and decisions; **EGA Skills** selects trusted reusable agent instructions; **Linear** owns issues and status; **T3 Code** runs native coding work; **Helm** is the owner-facing control plane over those native threads; **Git/CI** own code and review evidence. **EGA House Runner** is a separate partially proven productivity-platform capability, not Helm's execution backend. Do not create a duplicate coordinator or silently blend the two.
- **Source priority.** Use live Git/Linear/deployment evidence for current state, project-local `AGENTS.md` and approved specs for implementation policy, and Atlas for durable rationale. Source timestamps/SHA matter. Atlas status is a dated snapshot, never a realtime authorization or rollout gate.
- **Shipping labels matter.** Code exists ≠ reviewed head ≠ merged ≠ deployed to DEV ≠ real E2E accepted ≠ Production accepted. Report the exact checked commit and limitations.
- **Approvals.** By default an agent may implement, test and prepare a PR; do not auto-merge, release, deploy, grant scopes or purchase services without explicit scope-specific authorization. A direct owner instruction can authorize a bounded exception.
- **User interaction and prompts.** Answer first, then explain why; be brief and direct. For setup/troubleshooting, give one actionable step and check its result before the next. For coding-agent tasks, supply an executable goal, source context, scope, acceptance criteria and explicit **do not touch** boundaries. Use English for code/docs; French for professional applications where appropriate.
- **Weekly review.** Friday is the user's broader weekly check of unfinished work, deadlines, blockers and next priorities. Sunday's Atlas documentation/lint maintenance is a separate housekeeping cadence; neither proves project acceptance.
- **Bound verification.** After repeated failures, inspect logs, the Atlas and exact current state rather than repeating the same expensive commands or agent reviews; escalate unresolved blockers with evidence.
- **Keep it lean.** Prefer existing free/simple tools; resist scope creep. Use the VM's verified `heavy -- <command>` guard for installs/builds/heavy checks when its wrapper is available, avoiding competing expensive jobs. Don't assume it exists on another machine.
- **Finish with state.** After changing code, decisions or status, update the existing Atlas page, check `now.md` and `index.md`, append `log.md`, and commit. No raw agent dumps or private credentials in public docs.

- **Shared execution machine.** For Ubuntu VM work, apply [Agent runtime VM](agent-runtime-vm.md) hygiene before cleanup, resource-intensive checks or authentication changes.

## Gotchas and lessons

- Never call an untested implementation “done”; exercise the real path and its failure/authorization cases. Tests/CI may pass while credentials, browser behavior, migration state or tenant isolation remain unproven.
- When diagnosing failed agent changes, classify **observation → hypothesis → first reproducible divergence → minimal fix → independent review → fresh runtime test**. Label the result **static**, **merged**, **runtime-proven** or **end-to-end-proven**; don't infer one from another. Capture only sanitized evidence in public Atlas.
- Long polling/sleep loops, full test reruns and simultaneous VM builds waste execution time. Diagnose root cause, select focused checks, then run the relevant full gate once.
- Shared DEV migration history is not a scratch database. See [Decision 0003](../decisions/0003-gymtrack-shared-dev-migrations.md); do not reset/replay applied SQL, casually apply branch SQL, or confuse DB readiness with app acceptance.
- Approval policy has varied by project and date in older conversations. Use the latest explicit task/project policy rather than promoting an old one to a universal rule.

## Open questions

- A bulk export/index of 1,000 chats is not accessible in this session. Future full-history ingestion requires a user-provided export; reconcile contradictions before upgrading inferred patterns to confirmed decisions.
- Confirm any project-specific merge or resource permission before write actions outside the Atlas.

## Links

- [Atlas agent contract](../../AGENTS.md)
- [Agent runtime VM](agent-runtime-vm.md)
- [Gymtrack](../projects/gymtrack.md), [Helm](../projects/helm.md), [EGA Skills](../projects/ega-skills.md), [EGA House](../projects/ega-house.md)
- [Atlas update skill](../../.agents/skills/atlas-update/SKILL.md)
