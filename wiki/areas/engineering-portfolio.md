---
type: area
status: active
updated: 2026-10-23
related: [agent-runtime-vm, helm, ega-house]
---

# Engineering portfolio

A compact inventory of EGA codebases outside the current priority queue. Presence in GitHub is not evidence of active work, deployment or acceptance. Do not create parallel trackers or revive these projects without an explicit decision.

## Current state

| Repository | Verified purpose and documented maturity | Last GitHub push |
|---|---|---|
| [Token Observatory](https://github.com/egawilldoit/token-observatory) | Next.js/Supabase token-use telemetry; README specifies conservative cross-machine snapshot deduplication and private raw imports. Live service status not verified. | 2026-10-02 |
| [VM Updates Scripts](https://github.com/egawilldoit/vm-updates-scripts) | Small source archive for guarded native Codex, OpenCode, Hermes and T3 updates. Installed VM runtime is separate; see [Agent runtime VM](../concepts/agent-runtime-vm.md). | 2026-10-09 |
| [ShipLoop](https://github.com/egawilldoit/ShipLoop) | Foundation/specifications for AI-assisted delivery; README says no product web app or worker is delivered. [Helm](../projects/helm.md) is the newer native-T3 control-plane workstream. | 2026-10-09 |
| [Hermes Mobile](https://github.com/egawilldoit/hermes-mobile) | Expo Android control UI, typed contracts and guarded Fastify sidecar; README stages read-only pilot before approvals or production writes. | 2026-07-27 |
| [Back15](https://github.com/egawilldoit/Back15) | Local-first Expo time-log prototype; README says only starter screens/specifications exist, not real tracking or notifications. | 2026-09-23 |
| [AI Quota Pool Tracker](https://github.com/egawilldoit/AI-Quota-Pool-Tracker) | Older quota-related repository whose README remains the generic Next.js starter; functionality and deployment not verified. | 2026-06-02 |

GitHub push dates indicate repository activity only; they are not active-project declarations. Latest source checks: 2026-10-23.

## Decisions

- Keep these as **inventory**, not additional current priorities in [now.md](../../now.md).
- Prefer the established Token Observatory codebase when investigating recorded token usage; first recheck its actual deployment and data scope. Do not treat older starter projects as implemented alternatives.
- Existing agent service/updater tools are separate from product delivery. Preserve authority boundaries; avoid building replacements without an identified missing requirement.

## Open questions

- Which, if any, deserves an explicit owner-approved resume or archive decision?
- Which of the READMEs still matches deployed runtime, if any? No fresh runtime check was performed during this inventory.

## Next steps

1. Keep current job search, Gymtrack, Helm and EGA Skills priorities unchanged.
2. When a specific repository becomes relevant, fetch its current branch and agent rules; verify runtime before updating its status.
3. Create a dedicated project page only when a substantial workstream is actually resumed.

## Links

- [Current priorities](../../now.md)
- [Agent runtime VM](../concepts/agent-runtime-vm.md), [Helm](../projects/helm.md), [EGA House](../projects/ega-house.md)
