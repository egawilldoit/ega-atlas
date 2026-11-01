---
type: now
updated: 2026-11-01
---

# Now

EGA House merge/CI and EGA Skills release checked 2026-11-01; Gymtrack 2026-10-30; other snapshots 2026-10-25. Verify current issue, Git and runtime state before acting.

## Priorities (in order)

1. **Full-time job:** DevOps/Cloud, then CRM; Rabat/Casablanca. Working target: five quality applications daily. [Job hunt](wiki/areas/job-hunt.md).
2. **Gymtrack:** Sprint 2 closed by owner acceptance; plan Sprint 3 deferred verification and staff-policy work. [Gymtrack](wiki/projects/gymtrack.md).
3. **Helm, then EGA Skills:** certify Helm v0.2's real DEV journey; EGA Skills MCP release is live; separate owner/client acceptance remains. [Helm](wiki/projects/helm.md) · [EGA Skills](wiki/projects/ega-skills.md).

## Active work

- **Gymtrack:** [GYM-71](https://linear.app/egawilldoit/issue/GYM-71) Done with deferred Sprint 3 verification/deletion. Observe SDK [GYM-47](https://linear.app/egawilldoit/issue/GYM-47) merged; config, hosted DEV acceptance and Production enablement remain backlog. [Delivery report](wiki/projects/gymtrack.md). Full security certification and Production approval outstanding.
- **Helm:** [PRs #35–#39](https://github.com/EGA-BUILDS/helm/pulls?q=is%3Apr+is%3Amerged) merged 2026-10-23. Vercel DEV serves `develop@90b3852`; Production serves security-patched **v0.1** `main@d17c4f5`. Backend/Convex alignment and real Linear → T3 → PR owner acceptance are not certified. [EGA-685](https://linear.app/egawilldoit/issue/EGA-685) Backlog; [EGA-768](https://linear.app/egawilldoit/issue/EGA-768) Production v0.2 HOLD.
- **EGA Skills:** 126-skill/TStack MCP release live; #193 and #194 delivered automatically from main. Latest canonical identity, main CI and authenticated production verification passed. [Release handoff](wiki/projects/ega-skills.md). Disposable recovery demonstration, separate Console/OpenCode acceptance and package/tag reconciliation remain open.
- **EGA House:** W0–W3, UI polish, Dublin config and [#339](https://github.com/egawilldoit/Ega-House-Platform/pull/339) persistent shell merged. Main CI green at `6b8d7092`; Production READY/Dublin reported, manual shell acceptance owner-reported. Authenticated E2E excluded/unverified; [#337](https://github.com/egawilldoit/Ega-House-Platform/pull/337) CPU/realtime remains open; 50% CPU savings unproven. [EGA House](wiki/projects/ega-house.md).

## Watch out

- A merged PR, a READY deployment and a real owner-accepted journey are three different states.
- Sprint closure is not full certification. GYM-75 is In Progress; permanent deletion still requires approved policy.
- Do not mistake Helm's Production security-only patch for v0.2 promotion.
