---
type: project
status: active
updated: 2026-10-23
related: [agent-delivery-workflow]
---

# EGA Skills

Governed skill registry, immutable release/resolution system, and read-only local/hosted MCP for coding agents. Current goal: certify and finish the 2.0 release, not expand scope.

## Current state

- [egawilldoit/ega-skills](https://github.com/egawilldoit/ega-skills) documents 2.0 as a **candidate**, with V1.0.1 previously shipped. 2.0 has governed intake, immutable releases, retained serving/rollback and hosted OAuth/MCP functionality.
- The Web Console Production deployment is reported READY and its `/readyz` passed with a 116-skill release; that proves deployment/health, **not owner browser authorization acceptance**. [EGA-731](https://linear.app/egawilldoit/issue/EGA-731) remains In Review; signed-in authorized skill read, signed-out denial and rollback check were still open in its latest evidence.
- EGA Skills web/hosted acceptance and security/release gates must be evaluated independently; PR status, preview success, merged code, deployed health and owner acceptance are distinct.
- [EGA-732](https://linear.app/egawilldoit/issue/EGA-732) hosted OpenCode skill resolution remains Backlog; do not claim all connected-client workflows shipped.

## Decisions

- Skills have immutable content versions and deterministic resolution; keep project/release locks explicit, never silently advance them.
- MCP serves **read-only** skill content; selecting a skill does not execute its scripts.
- Maintain clear workspace authorization, release identity and rollback. Use the approved shared database; do not create a second one for convenience.
- Optimize for verified useful skills (small selection, progressive disclosure), not a large catalog or extra orchestration.

## Open questions

- Have Production owner login → authorized skill and signed-out denial, plus rollback, been retested at the current deployment? EGA-731 does not record full acceptance.
- Which remaining 2.0 release evidence gates are open at the latest code/deployment SHA?

## Next steps

1. Complete EGA-731 Production owner/signed-out browser checks and record exact release/deployment evidence.
2. Reconcile remaining 2.0 release, client and security gates before declaring stable.
3. Verify the EGA-732 hosted MCP → OpenCode workflow when it becomes the active scoped outcome.

## Links

- [Code and release docs](https://github.com/egawilldoit/ega-skills)
- [Console Production acceptance](https://linear.app/egawilldoit/issue/EGA-731)
- [Agent delivery workflow](../concepts/agent-delivery-workflow.md)
