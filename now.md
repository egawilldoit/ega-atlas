---
type: now
updated: 2026-10-25
---

# Now

Priorities and source snapshot checked 2026-10-25. Verify live issue, Git and deployment state before acting.

## Priorities (in order)

1. **Full-time job:** DevOps/Cloud, then CRM; Rabat/Casablanca. Working target: five quality applications daily. [Job hunt](wiki/areas/job-hunt.md).
2. **Gymtrack:** finish Sprint 2 functional acceptance in hosted DEV, then remaining staff-policy work. [Gymtrack](wiki/projects/gymtrack.md).
3. **Helm, then EGA Skills:** certify Helm v0.2's real DEV journey; finish EGA Skills owner/client acceptance. [Helm](wiki/projects/helm.md) · [EGA Skills](wiki/projects/ega-skills.md).

## Active work

- **Gymtrack:** GYM-83, GYM-89 and GYM-96 are Done with code merged to `develop`; repair MR !115 also merged. [GYM-88](https://linear.app/egawilldoit/issue/GYM-88) is In Progress: database readiness does **not** prove hosted Auth/mail/browser acceptance. [GYM-95](https://linear.app/egawilldoit/issue/GYM-95) and [GYM-14](https://linear.app/egawilldoit/issue/GYM-14) have draft MRs. GYM-86 remains Todo, blocked by unapproved deletion policy.
- **Helm:** [PRs #35–#39](https://github.com/EGA-BUILDS/helm/pulls?q=is%3Apr+is%3Amerged) merged 2026-10-23. Vercel DEV serves `develop@90b3852`; Production serves security-patched **v0.1** `main@d17c4f5`. Backend/Convex alignment and real Linear → T3 → PR owner acceptance are not certified. [EGA-685](https://linear.app/egawilldoit/issue/EGA-685) Backlog; [EGA-768](https://linear.app/egawilldoit/issue/EGA-768) Production v0.2 HOLD.
- **EGA Skills:** published v2.0.0 versus main package 2.0.1 remains unreconciled; Production Console READY, owner authorization [EGA-731](https://linear.app/egawilldoit/issue/EGA-731) In Review and OpenCode [EGA-732](https://linear.app/egawilldoit/issue/EGA-732) Backlog.
- **EGA House:** W0–W3 clean-room UX upgrade backlog scoped in Linear, **not implemented** on `main`. Planning resumed, but no promotion into the above delivery queue. [EGA House](wiki/projects/ega-house.md).

## Watch out

- A merged PR, a READY deployment and a real owner-accepted journey are three different states.
- GYM-75's Done label contradicts its still-missing privacy/product signoff; do not implement GYM-86 irreversible deletion.
- Do not mistake Helm's Production security-only patch for v0.2 promotion.
