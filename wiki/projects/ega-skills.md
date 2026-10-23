---
type: project
status: active
updated: 2026-10-23
related: [agent-delivery-workflow]
---

# EGA Skills

Governed skill registry, immutable release/resolution system, and read-only local/hosted MCP for coding agents. Current goal: certify and finish the 2.0 release, not expand scope.

## Current state

- [egawilldoit/ega-skills](https://github.com/egawilldoit/ega-skills) has a **published non-prerelease [v2.0.0 GitHub Release](https://github.com/egawilldoit/ega-skills/releases/tag/v2.0.0)** (2026-09-23), while current `main` `package.json` identifies **2.0.1** (checked 2026-10-23). The README still calls 2.0 a “candidate”: that wording is stale/misaligned with published artifacts. A release/tag/package version is **not** proof of hosted Console owner acceptance. 2.x includes governed intake, immutable releases, retained rollback and hosted OAuth/MCP.
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
- Which exact 2.0.1 source/artifact digest is currently promoted and what is its release/tag status? The latest **published GitHub Release returned by the repository API was v2.0.0**, despite main identifying 2.0.1. Reconcile release notes/readme with actual promoted artifacts.
- Which remaining 2.x release evidence gates are open at the latest code/deployment SHA?

## Next steps

1. Complete EGA-731 Production owner/signed-out browser checks and record exact release/deployment evidence.
2. Reconcile current 2.0.1 package, published release/tag and serving digest; then close remaining client/security gates before declaring the *hosted experience* accepted.
3. Verify the EGA-732 hosted MCP → OpenCode workflow when it becomes the active scoped outcome.

## Links

- [Code and release docs](https://github.com/egawilldoit/ega-skills)
- [Console Production acceptance](https://linear.app/egawilldoit/issue/EGA-731)
- [Agent delivery workflow](../concepts/agent-delivery-workflow.md)
