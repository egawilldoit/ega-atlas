---
type: now
updated: 2026-10-23
---

# Now

What matters this week. Snapshot checked 2026-10-23; refresh live issue/PR status before acting.

## Priorities (in order)

1. **Full-time job.** DevOps/Cloud first, then CRM (SAP, Salesforce); Rabat/Casablanca. Working target: five applications per day. See [Job hunt](wiki/areas/job-hunt.md).
2. **Gymtrack.** Finish Sprint 2 onboarding/staff/RBAC and certify actual hosted DEV behavior. See [Gymtrack](wiki/projects/gymtrack.md).
3. **Helm, then EGA Skills.** Complete Helm v0.2's DEV workflow without promoting Production; finish EGA Skills release/console acceptance before declaring stable. See [Helm](wiki/projects/helm.md) and [EGA Skills](wiki/projects/ega-skills.md).

Other projects remain outside this queue unless explicitly resumed. See the [engineering portfolio](wiki/areas/engineering-portfolio.md) for verified repository inventory.

## Active work

- **Gymtrack:** GYM-84 merged/Done ([MR !111](https://gitlab.com/trackgym101/gymsas/-/merge_requests/111)); [GYM-83](https://linear.app/egawilldoit/issue/GYM-83) remains a draft; [GYM-88](https://linear.app/egawilldoit/issue/GYM-88) functional QA In Progress. GYM-96 setup-only gap Todo; GYM-86/89 Todo.
- **Helm:** v0.2 DEV baseline `b85a8e3` remains unchanged. Draft [#35](https://github.com/EGA-BUILDS/helm/pull/35) safe native-write target, [#36](https://github.com/EGA-BUILDS/helm/pull/36) mounted execution review, [#37](https://github.com/EGA-BUILDS/helm/pull/37) docs/UX, and **security patches [#38](https://github.com/EGA-BUILDS/helm/pull/38) (develop), [#39](https://github.com/EGA-BUILDS/helm/pull/39) (main)** are CI-green but **unmerged**. Prioritize the Next.js security review. Linear OAuth, safe isolated write target and full EGA-685 acceptance remain open; Production v0.2 promotion held.
- **EGA Skills:** v2.0.0 published; main package 2.0.1. Production console deployed, but owner sign-in/authorization and release-version reconciliation remain open.

## Watch out

- Helm's deployed code remains on Next.js 16.4.0, affected by October 2026 security advisories. Security-only patching of `main` is **not** authorization to promote v0.2.
- Linear owns ticket state, Git owns code, and real runtime evidence owns acceptance. A green draft PR is not deployed functionality.
- Source material is incomplete: the October 23 context backfill did **not** inspect 1,000 full chat transcripts. Do not fabricate missing decisions or treat old statuses as current.
