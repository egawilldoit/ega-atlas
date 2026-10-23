---
type: project
status: active
updated: 2026-10-23
related: [helm, agent-delivery-workflow]
---

# Gymtrack

Multi-tenant gym-management SaaS built with two teammates. Finish Sprint 2 onboarding, staff and custom RBAC; prove the complete first-Owner journey in hosted DEV.

## Current state

- Canonical code: private [GitLab trackgym101/gymsas](https://gitlab.com/trackgym101/gymsas), Next.js web + NestJS API + Supabase/Auth. The active integration target is protected `develop`, even though GitLab's repository default remains older `main`. Fetch current `develop` before any implementation.
- Sprint 2 Book a Demo, Platform Admin review, Owner provisioning/credentials/first site, foundational custom roles and several domain permission checks are recorded Done in [Linear GYM](https://linear.app/egawilldoit/team/GYM). Done/merged does not certify hosted behavior.
- [GYM-72](https://linear.app/egawilldoit/issue/GYM-72) lifecycle *design* reconciliation is Done (merged [MR !110](https://gitlab.com/trackgym101/gymsas/-/merge_requests/110)); a separate setup-only access implementation remains [GYM-96](https://linear.app/egawilldoit/issue/GYM-96) Todo.
- [GYM-88](https://linear.app/egawilldoit/issue/GYM-88) records four additional migrations applied to shared DEV on 2026-10-23 and DB readiness passed. Functional Auth/mail/browser, multi-tenant/RBAC and full end-to-end QA remain **not accepted**.
- [GYM-84](https://linear.app/egawilldoit/issue/GYM-84) role-impact preview/edit is **Done** and [MR !111](https://gitlab.com/trackgym101/gymsas/-/merge_requests/111) **merged** at 2026-10-23 08:15 UTC into `develop` (`4773f44a`). This is merged code, not hosted functional acceptance.
- [GYM-83](https://linear.app/egawilldoit/issue/GYM-83) staff creation remains **In Progress** with open draft [MR !112](https://gitlab.com/trackgym101/gymsas/-/merge_requests/112) (observed head `8efb8a66`). [GYM-89](https://linear.app/egawilldoit/issue/GYM-89) staff role/sites reassignment and [GYM-86](https://linear.app/egawilldoit/issue/GYM-86) permanent deletion: Todo.

## Decisions

- Visitor onboarding is **Book a Demo**, not open public signup. Platform Admin reviews applications; an approved Owner completes credential change then creates the first site. Exactly one protected Owner in Sprint 2; no ownership transfer.
- D6/D7: established staff keep authorized historical read access when suspended; new provisioning users only receive setup/credential/status/recovery access. The latter still needs GYM-96 enforcement.
- Application rejection: no automatic rejection email, no reopen/reverse; resubmission follows duplicate rules. See [GYM-72](https://linear.app/egawilldoit/issue/GYM-72).
- Custom RBAC must enforce **live** role/site permissions (including existing JWTs); separation between role edit [GYM-84](https://linear.app/egawilldoit/issue/GYM-84) and individual assignment [GYM-89](https://linear.app/egawilldoit/issue/GYM-89).
- Shared DEV database: migrations are append-only, apply through the authorized delivery protocol; never reset or replay historical migrations. Actual DB delivery is not functional QA.

## Open questions

- [GYM-75](https://linear.app/egawilldoit/issue/GYM-75) has status **Done**, but the issue description (last substantive design record) still explicitly says product/privacy signoff is **missing** for retention, anonymization, cross-gym identity and irreversible deletion. This is a **status/evidence conflict**, not approval. [GYM-86](https://linear.app/egawilldoit/issue/GYM-86) stays blocked until explicit policy signoff is linked; do not implement irreversible deletion from the Done label.
- Which end-to-end hosted DEV Auth/mail/browser scenarios have live evidence, beyond database migration readiness? [GYM-88](https://linear.app/egawilldoit/issue/GYM-88) remains open.

## Next steps

1. Rebase/revalidate draft MR !112 (GYM-83) against `develop` after merged GYM-84; review the exact updated head and accept only after required gates and authorization.
2. Implement GYM-96 setup-only access with negative tests.
3. Complete GYM-88 hosted functional QA and attach actual runtime evidence.
4. Reconcile GYM-86 policy approval; then take GYM-89/86 in dependency order.

## Links

- [Canonical GitLab repository](https://gitlab.com/trackgym101/gymsas)
- [Linear GYM issues](https://linear.app/egawilldoit/team/GYM)
- Related: [Helm](helm.md), [Agent delivery workflow](../concepts/agent-delivery-workflow.md)
