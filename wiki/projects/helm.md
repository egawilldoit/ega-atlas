---
type: project
status: active
updated: 2026-10-23
related: [gymtrack, agent-delivery-workflow, 0002-native-t3-execution-ownership]
---

# Helm

Owner-facing orchestration control plane for **Linear issue → native T3 Code execution → thread/recovery → verified GitHub PR**. Linear owns issues, T3 owns agent execution and worktrees, and Helm coordinates. No second executor or automatic merge.

**Snapshot: 2026-10-23.** This is a current handoff, not a release certification. Live Git/CI and Vercel status were rechecked; PR descriptions and local test totals are reported as author evidence unless independently verified. Secrets and private runtime payloads are deliberately excluded.

## Current state

### Bottom line

**v0.1 Production is deployed; v0.2 is implemented on DEV in part, but is not release-accepted.** The latest three functional PRs and two security PRs are reviewable drafts, not merged or deployed. The full Linear → T3 → PR owner journey remains unproven.

| Surface | Current confirmed state | Evidence |
|---|---|---|
| Code, DEV | `develop` at `b85a8e3010d5c85b0b0b8f25709f6140901bc132` | [Helm develop](https://github.com/EGA-BUILDS/helm/tree/develop) |
| Code, Production | `main` at `0e863c410cf5b0eba438d0e656794fce366880b6` (v0.1) | [Helm main](https://github.com/EGA-BUILDS/helm/tree/main) |
| Vercel DEV | **READY**, serving `develop` / `b85a8e3` | [DEV](https://dev-helm.egawilldoit.online); verified via Vercel on 2026-10-23 |
| Vercel Production | **READY**, serving `main` / `0e863c4` | [Production](https://live-helm.egawilldoit.online); verified via Vercel on 2026-10-23 |
| Convex DEV | Functions synchronized to `hardy-warthog-60` after PR #34 | Owner's successful `convex dev --once` receipt, 2026-10-22; not independently redeployed in this pass |
| T3 OAuth | A real **Operate** connection was authorized and backend-verified; 19 projects visible in the loaded page | Owner's 2026-10-22 DEV screenshots |
| T3 dual-tier inventory | Latest screenshot shows **one Operate row**; simultaneous Read-only + Operate rows **not** live-proven | [PR #34](https://github.com/EGA-BUILDS/helm/pull/34) tests coexistence; runtime screenshot does not |
| Linear OAuth / mappings | Code implemented, but no successful owner Linear OAuth consent or mapped project has been shown | Earlier `Linear connection is unavailable` runtime failure |
| Native write journey | Implemented; not mounted on current DEV and **not authorized for real write testing** | Draft [#35](https://github.com/EGA-BUILDS/helm/pull/35) and [#36](https://github.com/EGA-BUILDS/helm/pull/36) |
| v0.2 release | **CODE REVIEW / DEV ACCEPTANCE BLOCKED. Production promotion HOLD.** | [EGA-685](https://linear.app/egawilldoit/issue/EGA-685), [EGA-768](https://linear.app/egawilldoit/issue/EGA-768) |

**Do not equate:** green CI with merge; merged code with deployed code; an OAuth Operate grant with a safely isolated write target; browser mocks with real hosted execution; GitHub PR visibility with approval or merge.

### Open draft PRs (heads rechecked 2026-10-23)

| PR | Base | Latest head | What it changes | Current gate |
|---|---|---|---|---|
| [#35 — native write target](https://github.com/EGA-BUILDS/helm/pull/35) | `develop` | `38c6242` | Fails closed unless an approved exact T3 endpoint and environment match the stored Operate grant; launch/send/interrupt | Independent security review, merge, DEV deployment and **disposable target configuration** |
| [#36 — issue execution UI](https://github.com/EGA-BUILDS/helm/pull/36) | `develop` | `9a6256d` | Mounts existing `ExecutePanel` from a verified Linear issue; prevents stale detail selection and duplicate client clicks; browser regression tests | Independent integration review, merge, DEV deployment; real Linear OAuth |
| [#37 — docs and error label](https://github.com/EGA-BUILDS/helm/pull/37) | `develop` | `f50d151` | Real README, release traceability, historical corrections, `Connection error` display fix | Review and reconcile overlapping `Dashboard.tsx` edits from #36 when merged |
| [#38 — Next.js security on develop](https://github.com/EGA-BUILDS/helm/pull/38) | `develop` | `04aa9ea` | `next` and `eslint-config-next` 16.4.0 → 16.4.2; lockfile | **Urgent** security review and owner-approved merge into `develop` |
| [#39 — Next.js security on main](https://github.com/EGA-BUILDS/helm/pull/39) | `main` | `215611b` | Same minimal patch for v0.1 Production branch, without v0.2 changes | **Urgent** review; explicit Production merge/deployment approval required |

Each of these **five** heads had four completed successful GitHub check runs (Node 22, Node 24, frontend browser, Vercel Preview Comments) at verification time. All five remained **draft**, with no submitted PR reviews. Green checks do not substitute for a security review or end-to-end acceptance. Their authors reported focused local test/build runs; verify current heads and retest after any rebase.

Old open [#3](https://github.com/EGA-BUILDS/helm/pull/3), [#4](https://github.com/EGA-BUILDS/helm/pull/4), and [#5](https://github.com/EGA-BUILDS/helm/pull/5) target the obsolete `integrate/ega-677-678-foundation` branch. #3 contains older isolated-dispatch modules absent on `develop`, superseded architecturally; #4/#5 have evolved equivalents in `develop`. **Do not merge them directly.** [#28](https://github.com/EGA-BUILDS/helm/pull/28) was closed without direct merge; its Linear-sync substance was incorporated into [#29](https://github.com/EGA-BUILDS/helm/pull/29).

### Implemented modules versus owner-visible acceptance

| Requirement | Existing implementation | Current acceptance |
|---|---|---|
| R01 Owner sign-in | Clerk + Convex owner auth / `OwnerDashboard` | Owner signed into DEV; working login observed |
| R02 Native T3 OAuth | `t3OAuth`, OAuth routes; [#33](https://github.com/EGA-BUILDS/helm/pull/33) and [#34](https://github.com/EGA-BUILDS/helm/pull/34) merged | **Operate LIVE**, exact scopes validated; dual-tier coexistence still pending |
| R03 Linear OAuth | `linearOAuth`, Linear callback/start routes | **BLOCKED**: no owner Linear connection proven |
| R04 Mappings | `projectMappings`, `MappingsPanel` | **BLOCKED** by R03; no live mapping proven |
| R05 Issue browser | `linearIssues`, `IssuesPanel` | Browser/code tests only; live mapped issue not proven |
| R06 Prompt review | `ExecutePanel`, prompt composition, preflight | On `develop` **unmounted**; draft #36 mounts it |
| R07 Per-execution model | Catalog-aware selection / preflight in `ExecutePanel` | Code tests only; real owner-browser path pending #36 |
| R08 Native worktree launch | `t3Launch`, durable attempts/recovery | No hosted disposable launch accepted; draft #35 adds target gate |
| R09 Thread↔issue | `linearAssociation`, `ManualLinkControl` | Code tests; real cross-system binding pending |
| R10 Linear status sync | `linearSync`, triggers, retry ledger, crons | Code tests; requires connected Linear + accepted launch |
| R11 Follow-up / interrupt | `t3Send`, `t3Interrupt`, attention state | Code tests; live isolated writes pending approval |
| R12 PR and attention | `VerifiedPullRequestPanel`, `verifiedPullRequest` | Code tests; real GitHub PR verification not accepted |

The original v0.2 backend has meaningful duplicate-attempt, ambiguous-acknowledgement, stale-owner, read-only-write and Linear-conflict guards. Draft #36 adds mounted-browser tests for reload recovery and two-tab racing. These are **test evidence**, not proof that live DEV is safe to mutate.

### Two verified integration gaps and their proposed repairs

1. **P0: Native T3 write destination.** Current `develop` native write actions use `operateToolCall(secret.endpoint, …)` with the saved Operate connection. The separate `T3_MCP_URL_ISOLATED` only controls the older `convex/dispatch.ts` path. This means the real Operate grant is **not automatically a disposable DEV target**. Draft #35 requires `T3_NATIVE_WRITE_ENDPOINT` and `T3_NATIVE_WRITE_ENVIRONMENTS` on Convex; matching is exact, and missing/mismatched configuration refuses launch/send/interrupt **before MCP calls**. It validates declared destination identity, **not** actual disposability, absence of Git remotes, or absence of Production credentials. The owner must attest and verify those separately. **Until #35 is merged and deployed, the new write gate is not present on DEV. No real write acceptance on the current relay.**
2. **P1: The execution panel was unreachable.** `OwnerDashboard` currently renders the T3 dashboard, mappings, and Linear issue browser but not `ExecutePanel`. Draft #36 connects a selected issue's authoritative project/team to an enabled mapping and mounts the existing review; it separates detail request generations so slow issue A cannot overwrite issue B, and adds click/race/reload browser tests. It does not create another execution engine. The current DEV UI remains unchanged until #36 merges and deploys.

**P3:** Draft #37 changes `Connection error: Unknown` on a healthy grant to `None — no current error`, while still showing true error codes.

### Linear OAuth: exact unclosed dependency

The prior error `Linear connection is unavailable` is emitted when the owner has **zero saved Linear OAuth connections** in `projectMappings`. This is not proof of a malformed token: expired/revoked/wrong-workspace cases have different paths. The earlier server-side `LINEAR_API_KEY` read probe, and ChatGPT's own Linear connector, do **not** establish a saved Helm Linear OAuth connection.

Owner flow still required: configure the Linear OAuth app with `https://dev-helm.egawilldoit.online/api/linear/callback`; configure DEV Convex's OAuth client and encryption-key variables using secret-safe procedures; complete browser consent; verify one connected workspace; create one project/team mapping with started state and branch choices; browse a real issue. **No actual Linear mutation should precede a separately approved disposable workflow test.**

### Security: Next.js patch is now the most urgent review

Both deployed branches still use their old commit baselines, whose dependency manifests pin `next@16.4.0`. GitHub's October 2026 advisories mark that release affected by two critical issues ([GHSA-2464-fhr4-w9xw](https://github.com/advisories/GHSA-2464-fhr4-w9xw) and [GHSA-673q-mjm6-r5xr](https://github.com/advisories/GHSA-673q-mjm6-r5xr)) and one high ([GHSA-p3m5-59mp-jv5r](https://github.com/advisories/GHSA-p3m5-59mp-jv5r)). Patched range begins at **16.4.1**; drafts #38/#39 select **16.4.2**. They report clean Next.js audit results and first-attempt builds; remaining unrelated `braces` and `ip-address` advisories are documented and out of these PRs.

**Priority:** independently review #39 and #38 promptly. Do not confuse a security-only patch on `main` with authorizing v0.2 promotion. Merging #39 may trigger Production deployment, which still requires explicit owner approval and post-deploy verification. No Production patch has landed yet.

### Test and release evidence

- **Merged #34 baseline:** backend 725, frontend 399, browser 83 passed; actual hosted Vercel + Convex DEV and T3 Operate authentication verified separately.
- **Draft #35:** author reports backend **758** passing tests on updated head; denied-write test cases assert zero MCP calls before refusal. Required Node 22/24 and browser check runs green at current head.
- **Draft #36:** author reports frontend **403**, browser **91** at the extended head; mounted UI, double-click, stale issue, reload and multi-tab browser coverage. Check runs green.
- **Draft #37:** author reports frontend **403**, browser **83**, build/typecheck/lint pass. Check runs green.
- **Draft #38:** author reports baseline backend **725**, frontend **403**, browser **83**, audit no Next.js advisories, first-attempt build. Check runs green.
- **Draft #39:** author reports Production-baseline backend **191**, frontend **186**, browser **40**, first-attempt build. Check runs green.
- All five PRs have **no independent submitted review** at the time of this report. Verify exact head before any merge and again after fixing overlaps.

Only the merged commit has been deployed to stable DEV. No changes from #35–#39 are live on their target branch deployments.

### Owner actions and dependencies

**First, secure and integrate the code:**
1. Independently review Next.js security [#39](https://github.com/EGA-BUILDS/helm/pull/39) (main) and [#38](https://github.com/EGA-BUILDS/helm/pull/38) (develop); obtain explicit merge and deployment approval, and verify fixed versions on the deployed artifacts.
2. Review [#35](https://github.com/EGA-BUILDS/helm/pull/35) for complete native-write coverage, exact configuration validation, no credential fallback, and genuine fail-closed behavior. Merge/deploy DEV only on approval; do not configure it to the real working T3 relay for disposable tests.
3. Review [#36](https://github.com/EGA-BUILDS/helm/pull/36) and [#37](https://github.com/EGA-BUILDS/helm/pull/37); rebase on latest `develop` after approved merges, reconcile their overlapping dashboard edits, run tests and checks on each new head, then merge/deploy DEV when authorized.

**Then provide the prerequisites:**
4. Establish a truly disposable T3 instance/project with no live remotes, private repositories or Production credentials. Verify endpoint and `t3_environment_read` identity; arrange an appropriate separate Operate authorization **without replacing an existing grant blindly**; set `T3_NATIVE_WRITE_ENDPOINT` and `T3_NATIVE_WRITE_ENVIRONMENTS` on **Convex DEV only**. Do not print values in transcripts.
5. Configure Helm's own Linear OAuth on Convex DEV, complete owner consent, and create one safe mapped Linear project/team. Resolve the missing-connection error with real inventory evidence.
6. Configure limited **read-only** GitHub PR verification where necessary; verify repository/branch identity before trusting an observed PR.
7. Obtain separate owner approval for the exact disposable write acceptance sequence: one native launch, one follow-up, one interrupt, plus association/Linear status/PR checks. Keep Production out of scope.
8. Complete [EGA-685](https://linear.app/egawilldoit/issue/EGA-685) R01–R12 with date-stamped receipts, code-vs-live status, failure/recovery checks and go/no-go. Only then revisit [EGA-768](https://linear.app/egawilldoit/issue/EGA-768).

### Tracking and local operational boundaries

- Linear is the ticket authority. On the 2026-10-23 read, **25** Helm issues were returned; EGA-759/760/761/681/764/765/767/769 were still **In Review**, EGA-685 **Backlog**, EGA-768 **Backlog/HOLD**, and legacy EGA-679 **Todo** / EGA-698 **Backlog**. No Linear status changes were made in this audit.
- Legacy PR #3 should be closed as superseded only after owner review; #4/#5 likewise, with no wholesale merges. One PR title does not prove equivalent code.
- Build and test on the shared VM using isolated worktrees and the existing `heavy` coordination guard; no subagents by default. Stop any process that would mutate a live target without authorization.
- No agent should ask for raw tokens, pairing codes or secret-bearing `convex env list` output. Use variable-name-only checks and secure owner consent.
- `next build` previously encountered a security-advisory stop on 16.4.0. Patch PRs report that 16.4.2 builds pass on the first attempt; do not treat retrying an affected build as remediation.

## Decisions

- **Native T3 executes; Helm coordinates.** See [Decision 0002](../decisions/0002-native-t3-execution-ownership.md). Linear owns issue state; GitHub/CI own review and merge evidence.
- **Per-execution main model; no subagents by default.** Optional subagent/model remains a product input, not a supported native launch: the server currently rejects subagent-enabled launch. Do not claim the option works.
- **Native isolated worktrees, explicit base/target branch, owner-controlled launch.** T3 may produce a reviewable PR; Helm does not auto-merge.
- **Separate permissions and releases.** Read-only and Operate grants have distinct scopes. Supervised T3 consent grants the two Operate scopes; Full access is deliberately rejected. DEV code, actual DEV consent, safe write target, Production code, and Production approval are independent gates.
- **Fail closed on uncertain outcomes.** No ambiguous launch retry, cross-thread follow-up/interrupt, or silent conflict override. A confirmed protected target is a prerequisite for writes, not a license to perform them.

## Open questions

- When will #35–#39 receive independent reviews and owner merge/deployment approval? Which current `develop` head should each be rebased onto?
- Can the owner safely provision or reuse a genuinely disposable T3 instance **separate from the existing real Operate connection**, given current active-connection limits? Verify first; do not overwrite the working grant.
- Is Helm's Linear OAuth application configured on DEV and can it produce one saved owner connection? The earlier mapping error indicates no saved connection at that time.
- Which GitHub repository/branch is authorized for read-only PR verification in disposable acceptance? Do not infer from an unrelated repo.
- Are optional native subagents a future explicitly scoped delivery or intentionally deferred from v0.2? Currently launch rejects enabled subagents.
- What exact hosted R01–R12 receipts remain after #35–#39 merge and DEV deploy? A mocked browser race test is not hosted acceptance.
- When will stale Linear issues EGA-679/EGA-698 and obsolete PRs #3–#5 be reconciled without inventing acceptance?

## Next steps

1. **Security first:** review #39 (Production v0.1 patch) and #38 (DEV patch), approve only the explicitly intended merge/deployment, verify actual deployed Next.js version.
2. **Fail-closed integration:** review and approve #35; verify *all* native writes are guarded. Deploy to DEV before any acceptance writes.
3. **Real UI path:** review #36 and #37, resolve merge overlaps, ship the mounted issue execution path and docs to DEV; test authenticated browser behavior.
4. **Owner setup:** complete Helm Linear OAuth, mapping, read-only GitHub PR check and truly isolated T3 target authorization/configuration. Do not mutate real T3 work.
5. **Release gate:** approve a disposable EGA-685 full journey, verify R01–R12 and recovery, reconcile Linear tickets, and keep EGA-768 Production promotion **HOLD** until independently accepted.

## Links

- [Helm GitHub repository](https://github.com/EGA-BUILDS/helm) · [All PRs](https://github.com/EGA-BUILDS/helm/pulls)
- [DEV](https://dev-helm.egawilldoit.online) · [Production](https://live-helm.egawilldoit.online)
- [#35 native target](https://github.com/EGA-BUILDS/helm/pull/35) · [#36 execution UI](https://github.com/EGA-BUILDS/helm/pull/36) · [#37 documentation](https://github.com/EGA-BUILDS/helm/pull/37)
- [#38 DEV Next.js patch](https://github.com/EGA-BUILDS/helm/pull/38) · [#39 Production Next.js patch](https://github.com/EGA-BUILDS/helm/pull/39)
- [EGA-685 hosted acceptance](https://linear.app/egawilldoit/issue/EGA-685) · [EGA-768 Production HOLD](https://linear.app/egawilldoit/issue/EGA-768)
- [Agent delivery workflow](../concepts/agent-delivery-workflow.md) · [Gymtrack](gymtrack.md)
