---
type: project
status: active
updated: 2026-10-25
related: [helm, agent-delivery-workflow, 0003-gymtrack-shared-dev-migrations]
---

# Gymtrack

Multi-tenant gym-management SaaS. Current delivery goal: certify Sprint 2 onboarding, staff and live RBAC in hosted DEV without confusing code merge with functional acceptance.

## Current state

- Canonical code is private [GitLab trackgym101/gymsas](https://gitlab.com/trackgym101/gymsas); protected integration branch is `develop`, not the repository's older default `main`. Checked `develop@38812749` on 2026-10-25.
- Staff creation [GYM-83](https://linear.app/egawilldoit/issue/GYM-83) merged via [MR !112](https://gitlab.com/trackgym101/gymsas/-/merge_requests/112) on 2026-10-23. Role/site reassignment [GYM-89](https://linear.app/egawilldoit/issue/GYM-89) merged via [MR !113](https://gitlab.com/trackgym101/gymsas/-/merge_requests/113) the same day. Both are Done in Linear. Follow-up repair [MR !115](https://gitlab.com/trackgym101/gymsas/-/merge_requests/115) merged 2026-10-24. **These are merged code states, not hosted acceptance.**
- Provisioning setup-only authorization [GYM-96](https://linear.app/egawilldoit/issue/GYM-96) is Done; [MR !116](https://gitlab.com/trackgym101/gymsas/-/merge_requests/116) merged into `develop` 2026-10-25. It closes the earlier D6 implementation gap at code level; runtime negative cases remain part of acceptance.
- [GYM-84](https://linear.app/egawilldoit/issue/GYM-84) role-impact editing was previously merged in [MR !111](https://gitlab.com/trackgym101/gymsas/-/merge_requests/111). Earlier Book a Demo, approval, credential and first-site paths are also recorded Done in Linear; no end-to-end hosted verdict follows from those labels.
- [GYM-88](https://linear.app/egawilldoit/issue/GYM-88) remains **In Progress**. Its 2026-10-23 shared DEV migration delivery and database readiness passed, but live Auth/mail/browser, multi-tenant and RBAC end-to-end checks are **not accepted**.
- Local QA preparation [GYM-95](https://linear.app/egawilldoit/issue/GYM-95) is In Progress with **draft** [MR !117](https://gitlab.com/trackgym101/gymsas/-/merge_requests/117). Operator bootstrap [GYM-14](https://linear.app/egawilldoit/issue/GYM-14) is In Progress with **draft** [MR !119](https://gitlab.com/trackgym101/gymsas/-/merge_requests/119). Neither draft is merged or permission to execute provider operations.
- [GYM-86](https://linear.app/egawilldoit/issue/GYM-86) permanent staff deletion remains **Todo / blocked** by missing policy approval.

## Decisions

- Visitor onboarding uses **Book a Demo**, Platform Admin review, approved Owner credential change and first-site setup; no open public signup. One protected Owner; no ownership transfer in Sprint 2.
- D6/D7: new provisioning users have setup/credential/status/recovery access only; established staff retain authorized historical reads for suspended gyms. Verify live JWT and tenant boundaries, including after password change.
- Role/site assignments and effective custom RBAC are live authorization decisions, not stale JWT claims. Role definition changes and individual reassignment are distinct operations.
- Shared Supabase DEV migrations are serialized, checksummed and forward-fix only. See [Decision 0003](../decisions/0003-gymtrack-shared-dev-migrations.md); database readiness is not functional acceptance.

## Open questions

- [GYM-75](https://linear.app/egawilldoit/issue/GYM-75) is marked **Done**, but its description explicitly says retention, anonymization, cross-gym identity and irreversible deletion need product/privacy approval; [GYM-86](https://linear.app/egawilldoit/issue/GYM-86) also names this hard prerequisite. **Do not infer signoff from the status.**
- Which exact hosted DEV owner/staff/Auth/mail/browser and negative authorization scenarios have passed on the current deployed application/API/DB identities? GYM-88 is still open.
- What evidence and approval will promote the GYM-95 preparation harness and GYM-14 bootstrap command from reviewed code to safe operational use?

## Next steps

1. Complete independent exact-head review/CI for draft !117 and !119; merge only through normal authorized GitLab process. Do not run real bootstrap or provider calls as part of review.
2. Run separately authorized GYM-88 hosted DEV journeys with synthetic identities; capture sanitized real evidence for setup-only denial, credential changes, staff role/site updates and tenant isolation.
3. Resolve GYM-75 policy contradiction with explicit approval before starting GYM-86 deletion implementation.

## Links

- [Canonical GitLab repository](https://gitlab.com/trackgym101/gymsas) · [Linear GYM](https://linear.app/egawilldoit/team/GYM)
- [Helm](helm.md) · [Agent delivery workflow](../concepts/agent-delivery-workflow.md)
