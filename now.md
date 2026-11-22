---
type: now
updated: 2026-11-22
---

# Now

Source recheck 2026-11-22: newer code is merged in Gymtrack, EGA House and EGA Skills. Deployment, DEV acceptance and Production acceptance are separate, unverified gates.

## Priorities (in order)

1. **Full-time job:** DevOps/Cloud, then CRM; Rabat/Casablanca. Working target five quality applications daily. [Job hunt](wiki/areas/job-hunt.md).
2. **Gymtrack:** Sprint 2 accepted with deferred verification; plan Sprint 3 security/deletion and hosted QA. [Gymtrack](wiki/projects/gymtrack.md).
3. **Helm, then EGA Skills:** validate real DEV journey and canonical MCP/client acceptance. [Helm](wiki/projects/helm.md) · [EGA Skills](wiki/projects/ega-skills.md).

## Active work

- **Gymtrack:** [!134/!135](https://gitlab.com/trackgym101/gymsas/-/merge_requests/134) integrated 35-route redesign; [!165](https://gitlab.com/trackgym101/gymsas/-/merge_requests/165) theme/filters and [!167](https://gitlab.com/trackgym101/gymsas/-/merge_requests/167) modal forms merged to `develop@34548d38` on November 22. Current hosted auth/tenant QA, security and Production acceptance unverified.
- **Helm:** [#40–#47](https://github.com/EGA-BUILDS/helm/pulls?q=is%3Apr+is%3Amerged) merged to develop; historical DEV frontend receipt, Convex parity unverified. Production remains security-patched v0.1. [EGA-685](https://linear.app/egawilldoit/issue/EGA-685) and [EGA-768](https://linear.app/egawilldoit/issue/EGA-768) are **Canceled**, not accepted; identify successor approval gates.
- **EGA Skills:** [#203](https://github.com/egawilldoit/ega-skills/pull/203) compatibility merged to `main@dc5a74a9` November 18. Earlier November 3 release verifier failed. Current canonical release, Console/client and recovery acceptance unverified.
- **EGA House:** [#422–#424](https://github.com/egawilldoit/Ega-House-Platform/pulls?q=is%3Apr+is%3Amerged) web/Money/task simplification merged to `main@b0e30aca` November 22. Current CI, Production migrations, authenticated E2E and CPU savings unverified.

## Watch out

- Merged code, green CI, READY deployment, DEV-accepted and Production-accepted are different states.
- Sprint closure does not approve permanent deletion or Production rollout.
