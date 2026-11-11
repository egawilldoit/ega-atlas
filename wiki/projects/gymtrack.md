---
type: project
status: active
updated: 2026-11-11
related: [helm, agent-delivery-workflow, 0003-gymtrack-shared-dev-migrations, 0004-gymtrack-sprint-2-acceptance]
---

# Gymtrack

Multi-tenant gym-management SaaS. Sprint 2 delivered application-led onboarding, Owner activation, staff administration and custom action permissions in DEV. The owner closed the sprint on 2026-10-30 with explicit verification and deletion work deferred to Sprint 3.

## Current state

[GYM-71 is Done](https://linear.app/egawilldoit/issue/GYM-71). This records owner acceptance of delivered scope, **not full hosted security certification or production readiness**. [Decision 0004](../decisions/0004-gymtrack-sprint-2-acceptance.md) explains the changed closure boundary.

Canonical code: [GitLab trackgym101/gymsas](https://gitlab.com/trackgym101/gymsas), integration branch `develop`. The sprint was exercised on [DEV web](https://dev.gymtrack.space) and [DEV API](https://api-dev.gymtrack.space). Project remains active.

**Frontend redesign integrated (2026-11-11):** [MR !135](https://gitlab.com/trackgym101/gymsas/-/merge_requests/135) (persistent verified sidebar) merged into [MR !134](https://gitlab.com/trackgym101/gymsas/-/merge_requests/134) (full UI unification), then !134 merged into `develop@d7882f590230a1dd6c6be9c63f8aec5e96049401`. All 35 routes are accounted for: 33 styled UI routes and two preserved redirects. Final MR diff: 76 files, all inside `apps/web/src/app/**`. The [protected-branch CI](https://app.circleci.com/workflow/913b9701-90f0-4a3d-b7f7-a972991c054e) and Vercel web/API checks passed on the exact merge SHA. This establishes integration and delivery checks, **not** hosted QA, remote-revocation certification, or production promotion. See the [illustrated full release handoff](../../raw/gymtrack/2026-11-11-frontend-ui-unification-delivery.md).


**CI delivery (2026-11-02):** [MR !124](https://gitlab.com/trackgym101/gymsas/-/merge_requests/124) merged to `develop@a23dab5`: web, API unit, API HTTP E2E and delivery checks now run as four parallel CircleCI jobs, with a strict workflow-specific evidence contract and safer cache policy. The exact merged SHA passed [protected-branch validation](https://app.circleci.com/workflow/792a3dc9-6b7b-4c34-96bc-5b768af5ee32). The last pre-merge run was **205.4s**; seven small-executor pilot runs have a **provisional 216.8s median** versus the historical **328.97s** median. Billed credits and a stable new p50 remain unverified. [Full CI/CD report and 43-point T3 Code-derived comparison](../../raw/gymtrack/2026-11-02-circleci-performance-optimization.md). This is CI acceptance, not application Production certification.

Separate observability track (checked 2026-11-01): [GYM-47](https://linear.app/egawilldoit/issue/GYM-47) is Done and its opt-in NestJS Observe instrumentation merged to GitLab `develop@d0ecb46862566f8e4316d3a6f58cfd624f3f1008` on 2026-10-30. [GYM-48](https://linear.app/egawilldoit/issue/GYM-48) config delivery, [GYM-49](https://linear.app/egawilldoit/issue/GYM-49) hosted DEV privacy/trace acceptance and [GYM-50](https://linear.app/egawilldoit/issue/GYM-50) Production enablement remain Backlog. Merge does **not** establish enabled DEV tracing, accepted privacy behavior or Production activation.

### What Sprint 2 shipped

| Capability | Delivered behavior | Owning issues |
|---|---|---|
| Public intake | Book a Demo collects applicant name, email, phone and gym name; submission requests review rather than granting gym access | [GYM-76](https://linear.app/egawilldoit/issue/GYM-76) |
| Admin review | Internal application list/detail, approval and rejection; approval provisions an organization and protected Owner | [GYM-77](https://linear.app/egawilldoit/issue/GYM-77), [GYM-78](https://linear.app/egawilldoit/issue/GYM-78) |
| Owner credentials | Real emailed temporary credential, mandatory first password change and guarded onboarding | [GYM-73](https://linear.app/egawilldoit/issue/GYM-73), [GYM-79](https://linear.app/egawilldoit/issue/GYM-79) |
| First site and lifecycle | Owner creates first site after approval; organization can become Active; provisioning identities have setup-only access | [GYM-72](https://linear.app/egawilldoit/issue/GYM-72), [GYM-80](https://linear.app/egawilldoit/issue/GYM-80), [GYM-96](https://linear.app/egawilldoit/issue/GYM-96) |
| Custom RBAC | Owner-defined action permissions; exactly one custom role per staff account, scoped to assigned sites; compatible authorization foundation | [GYM-74](https://linear.app/egawilldoit/issue/GYM-74), [GYM-81](https://linear.app/egawilldoit/issue/GYM-81), [GYM-82](https://linear.app/egawilldoit/issue/GYM-82) |
| Staff onboarding | Owner creates staff with role/site assignments and emailed access; initial credential change required | [GYM-83](https://linear.app/egawilldoit/issue/GYM-83) |
| Shared role editing | Permission-change preview identifies impact before confirmation; authorization uses current grants | [GYM-84](https://linear.app/egawilldoit/issue/GYM-84) |
| Staff reassignment | Owner previews and changes role and complete site assignment set | [GYM-89](https://linear.app/egawilldoit/issue/GYM-89) |
| Staff deactivation | Owner revokes gym business access while retaining history | [GYM-85](https://linear.app/egawilldoit/issue/GYM-85) |
| Domain authorization | Action gates for Members, Plans, Memberships and existing finance reads/period generation | [GYM-90](https://linear.app/egawilldoit/issue/GYM-90)–[GYM-93](https://linear.app/egawilldoit/issue/GYM-93) |
| Signup compatibility | Retired public signup and legacy invitation cutover implementation | [GYM-87](https://linear.app/egawilldoit/issue/GYM-87) |
| QA support | Browser regression harness and reproducible account preflight preparation | [GYM-94](https://linear.app/egawilldoit/issue/GYM-94), [GYM-95](https://linear.app/egawilldoit/issue/GYM-95) |

Implementation delivery does not mean every negative or recovery case was exercised in hosted DEV. Live issue contracts remain authoritative; this page summarizes outcomes rather than reproducing private specifications.

### Permission contract

| Operation | Required custom-role grants |
|---|---|
| Member create/update | Respective action plus `members.read` |
| Plan reads | `plans.read`; creation/editing remain Owner-only |
| Membership create | `memberships.create`, `memberships.read`, `members.read`, `plans.read` |
| Membership update | `memberships.update` and `memberships.read`; custom staff limited to status |
| Period generation | `periods.generate`, `periods.read`, `memberships.read`, `members.read` |
| Payment reads | `payments.read` and `periods.read` |

Site-scoped access depends on live assignments. Permitted Plan reads are organization-wide and can remain available with zero assigned sites. Deactivation must remove both forms of business access. The action rollout configuration selects implemented actions; it is not a registry that needs an environment update for each new role.

### Manual DEV results accepted during the sprint

Evidence basis: screenshots reviewed during the owner-led journey, a supplied browser-agent report and explicit owner confirmations. No account identifiers or raw evidence are published here.

| Check | Result and evidence boundary |
|---|---|
| Public request → Admin approval → Owner provisioning | Happy path passed; provisioning UI was observed, not independently audited in the database |
| Real credential email → temporary login → mandatory password change | Fresh test-gym journey passed after repairs; not proof of all expiry/recovery scenarios |
| First site → Active organization/dashboard | Passed after the first-site migration |
| Owner role/staff setup and staff onboarding | Happy path passed |
| Owner and Staff workspace identity/site scope | Browser-agent report passed baseline identity, role, assigned site and refresh |
| Staff Member/Plan/Membership reads and permitted creation | Happy paths exercised; membership cancellation persisted after refresh |
| Owner-only Staff/Roles screens | Browser-agent report: Staff direct URLs denied without management data |
| Member update grant/revoke | Single affected staff preview; allowed edit persisted; fixture restored; removal hid edit and direct edit became read-only |
| Site removal/restoration | Existing staff browser lost Member access after site removal and regained it after restoration |
| Zero-site Plan access | Still readable under the permitted organization-wide grant, as expected |
| Deactivation | Staff Member and Plan direct pages denied after refresh |
| Cross-gym isolation | Owner explicitly reports denial from a different gym account; not independently reproduced here |
| Staff direct Plan creation | Owner explicitly reports denial; not independent API write proof |

Screenshots do not prove exact old-JWT continuity, comprehensive tenant isolation, RLS enforcement or every denied write. The initial signed-out tests reached Vercel Deployment Protection and therefore established only Vercel-gate behavior, not Gymtrack authentication.

### Repairs and delivery evidence

| Item | Recorded outcome | Limit |
|---|---|---|
| Admin approval repair [MR !120](https://gitlab.com/trackgym101/gymsas/-/merge_requests/120) | Earlier approval failure resolved in the tested journey | Not all approval races/retries tested |
| Password-change repair [MR !121](https://gitlab.com/trackgym101/gymsas/-/merge_requests/121) | Merged; addressed interrupted mutation completion/recovery and staff anchoring | Earlier affected identity recovery remains separate |
| First-site repair [MR !122](https://gitlab.com/trackgym101/gymsas/-/merge_requests/122) | Approval decision-version check repaired; migration applied; first site then worked | Not complete setup-negative coverage |
| Post-merge CI | Supplied delivery receipts report protected application/database validation passed for both repairs | CI is supporting evidence, not hosted acceptance |
| Hosted provider contract | Supplied report: disposable principal password update, receipt correlation, old password and two refresh sessions rejected, new password accepted, cleanup succeeded | Provider-specific test, not full application recovery acceptance |
| DEV database | Supplied guarded delivery receipt: 33 migrations through `20261029080543`, zero pending, catalog and restricted-runtime verification passed | Historical delivery receipt; no fresh live DB audit performed for this documentation update |
| Vercel | DEV deployments were READY; API redeployed after action rollout configuration | Exact currently served web/API source revisions and configuration parity remain deferred evidence |

The original Owner failure was an unfinished password-change mutation: application state recorded provider success but final completion was absent. Later requests refused to proceed. The original interruption's historical cause was not conclusively established; the successful fresh journey does not prove recovery of that earlier account.

The first-site failure had a separate approval-version eligibility defect. Fixing the password journey alone did not apply the required database migration.

### Scope carried into Sprint 3

- [GYM-88](https://linear.app/egawilldoit/issue/GYM-88): remaining hosted/security acceptance, reopened as Todo and relabeled Sprint 3. Original matrix retained. Reused because the workspace issue limit prevented a new issue.
- [GYM-75](https://linear.app/egawilldoit/issue/GYM-75): retention/deletion policy remains In Progress.
- [GYM-86](https://linear.app/egawilldoit/issue/GYM-86): permanent staff deletion remains Todo, blocked by policy approval.
- [GYM-97](https://linear.app/egawilldoit/issue/GYM-97): editable membership-date defaults, Todo. Enhancement, not an implemented Sprint 2 feature.

Deferred verification includes two-staff shared-role behavior and stale previews; actual role replacement and multiple-site reassignment with existing sessions; status-only membership writes; finance grants/co-grants; onboarding replay, approval concurrency, expiry, reissue and recovery; legacy/suspended compatibility; exact deployment/database evidence; required authenticated automated cases; mobile and keyboard checks. These are **deferred, not passed**.

### Explicitly outside delivered scope

Permanent staff deletion; member login/portal; ownership transfer; new cash collection, payment reversal and WhatsApp reminder workflows. Existing finance permission work does not deliver those backlog features. No production rollout or full security certification was approved by closing this sprint.

## Decisions

- The 43-point T3 Code CI checklist guided the analysis, **not 43 shipped changes**: job splitting and safe caching were delivered; faster database runners were tried and reverted; sharding, auto-cancel and selective path filtering are deferred. See the [item-by-item trace](../../raw/gymtrack/2026-11-02-circleci-performance-optimization.md#12-the-43-t3-code-derived-ci-optimization-techniques--gymtrack-outcomes).

- [Decision 0004](../decisions/0004-gymtrack-sprint-2-acceptance.md): accept the delivered Sprint 2 implementation and carry unexecuted acceptance/deletion work forward without fabricating passes.
- Application-led access replaces open signup. Exactly one protected Owner; Owner manages staff and roles.
- Current database membership, permissions and site assignments govern access; UI visibility and cached claims are insufficient.
- Shared DEV SQL remains serialized, checksummed and forward-fix only. See [Decision 0003](../decisions/0003-gymtrack-shared-dev-migrations.md).
- Keep product implementation, merge, CI, DEV delivery, hosted acceptance and production approval as distinct evidence states.
- Retain CircleCI for app validation; preserve all required test jobs, protected exact-SHA evidence and branch-scoped cache trust. The database `medium` experiment did not improve the critical path and was reverted (MR !124).

## Open questions

- Complete hosted acceptance of the merged frontend, especially multi-organization no-query navigation, role/session revocation, logout/login-as-different-user, default 404/failure clearing and Admin application detail. A page-fed sidebar projection preserves navigation, but is never an authorization source.

- Which deferred GYM-88 cases will be scheduled first in Sprint 3, using identified revisions and dedicated fixtures?
- What retention purpose/duration and cross-organization identity behavior will be approved before permanent deletion?
- Exact served application/configuration parity, complete authenticated automated evidence and earlier affected-account recovery remain unresolved.
- The previous Atlas page's 2026-10-25 draft/bootstrap observations were not reverified for unrelated work in this update.
- The seven-day post-cache-retention billed usage and a stable ten-run post-split p50/p90 are not yet measured; the database remains the CI bottleneck. Exact protected six-job results were not independently audited in Atlas despite a successful GitLab external workflow status.

## Next steps

1. Validate the merged frontend against a known deployed DEV SHA using disposable Owner/Manager/multi-organization fixtures; focus on sidebar persistence, denial and context reset. [Release handoff](../../raw/gymtrack/2026-11-11-frontend-ui-unification-delivery.md).

1. Plan Sprint 3 from GYM-88, GYM-75, GYM-86 and GYM-97; do not repeat already accepted manual checks without a regression reason.
2. Resolve deletion policy before implementing irreversible deletion.
3. Obtain separate evidence and authorization for any production release.
4. After enough normal runs accumulate, reconcile CircleCI credits/storage and compute a comparable post-split latency median. Keep database sharding deferred.

## Links

- [Gymtrack UI unification — full-MR audited handoff and evidence](../../raw/gymtrack/2026-11-11-frontend-ui-unification-delivery.md) · [MR !134](https://gitlab.com/trackgym101/gymsas/-/merge_requests/134) · [MR !135](https://gitlab.com/trackgym101/gymsas/-/merge_requests/135)

- [Sprint closure and accepted scope](https://linear.app/egawilldoit/issue/GYM-71)
- [Deferred acceptance and original matrix](https://linear.app/egawilldoit/issue/GYM-88)
- [Canonical code](https://gitlab.com/trackgym101/gymsas) · [Linear GYM](https://linear.app/egawilldoit/team/GYM)
- [CI/CD optimization delivery report](../../raw/gymtrack/2026-11-02-circleci-performance-optimization.md) · [Merged MR !124](https://gitlab.com/trackgym101/gymsas/-/merge_requests/124)
- [Helm](helm.md) · [Agent delivery workflow](../concepts/agent-delivery-workflow.md)

Source boundary: Linear states rechecked on 2026-10-30; delivery and browser outcomes summarized from the owner's authorized review session and supplied reports. This public page contains conclusions and source links, not private account data, credentials, raw chats, screenshots or runtime logs.
