---
type: project
status: active
updated: 2026-10-25
related: [gymtrack, agent-delivery-workflow, 0002-native-t3-execution-ownership]
---

# Helm

Owner-facing control plane for **Linear issue → native T3 Code execution → thread/recovery → verified GitHub PR**. Linear owns issues, T3 owns worktrees and execution, Helm coordinates. No second executor or auto-merge.

## Current state

**Checked 2026-10-25.** v0.2 code is merged to `develop` and its frontend is deployed to DEV; **full hosted v0.2 acceptance is still open**. Production remains v0.1 plus an independently approved security patch.

| Layer | Verified code/deployment state | Acceptance boundary |
|---|---|---|
| GitHub DEV | `develop@90b385287d2c575dee5c054b6ff663f93030427d` | Source integrated; not real E2E proof |
| Vercel DEV | READY, `dev-helm.egawilldoit.online` aliases deployment at the same `develop` SHA | Frontend deployed; Convex backend/version parity **not reverified** this pass |
| GitHub Production | `main@d17c4f575f04af111fe8b172fc7f2e046dbead27` | v0.1 with security-only patch, **not v0.2** |
| Vercel Production | READY, `live-helm.egawilldoit.online` aliases the same `main` SHA | Deployment READY; no new owner-browser acceptance claim |
| Product | [EGA-685](https://linear.app/egawilldoit/issue/EGA-685) Backlog, [EGA-768](https://linear.app/egawilldoit/issue/EGA-768) Backlog/HOLD | R01–R12 owner DEV journey and Production promotion **not accepted** |

### Merged integration and security changes

- [#35](https://github.com/EGA-BUILDS/helm/pull/35) merged 2026-10-23: native T3 launch/send/interrupt fail closed unless approved exact write endpoint/environment match. The guard verifies declared target identity; **it does not prove the target is disposable** or authorize real writes.
- [#36](https://github.com/EGA-BUILDS/helm/pull/36) merged 2026-10-23: mounts the existing `ExecutePanel` on verified Linear issue selection, with stale-request and duplicate-action browser guards. Mounted code and browser tests do **not** prove live Linear OAuth or native execution.
- [#37](https://github.com/EGA-BUILDS/helm/pull/37) merged 2026-10-23: README/release traceability and connection-error label correction.
- [#38](https://github.com/EGA-BUILDS/helm/pull/38) merged to `develop` and [#39](https://github.com/EGA-BUILDS/helm/pull/39) to `main` on 2026-10-23: Next.js and `eslint-config-next` 16.4.0 → 16.4.2. Both corresponding Vercel aliases now serve post-merge SHAs. This addresses the scoped Next.js patch, **not** every dependency/security risk or v0.2 Production approval.

### Unclosed owner-visible gates

- **T3 OAuth:** earlier owner DEV evidence confirmed a real Operate grant, but read-only + Operate simultaneous inventory and exact safe isolated destination are not fully certified. An Operate grant alone is not permission to write to a real worktree.
- **Linear OAuth:** code and callback exist; a successful saved owner connection, real mapped project/team, issue browser and accepted Linear state transition remain unproven. A separate API key or ChatGPT's Linear connection is not Helm's OAuth connection.
- **Native writes:** launch, follow-up, interrupt and thread recovery are implemented/tested, but the current **deployed Convex backend identity and disposable target configuration must be checked** before a separately approved real DEV write. Never point the guard at a working Production/real relay for convenience.
- **PR/acceptance:** live issue → selected model/prompt → isolated worktree → thread → unmerged PR verification, two concurrent threads, external-thread association and failure/recovery need dated browser/runtime evidence. Mock/browser CI is not owner acceptance.
- Older open [#3](https://github.com/EGA-BUILDS/helm/pull/3), [#4](https://github.com/EGA-BUILDS/helm/pull/4), [#5](https://github.com/EGA-BUILDS/helm/pull/5) target obsolete integration work. Do not merge wholesale without a separate reconciliation review.

## Decisions

- Native T3 owns execution; Helm only coordinates. See [Decision 0002](../decisions/0002-native-t3-execution-ownership.md).
- Per-execution main model; **no subagents by default**. Native subagent-enabled launch remains capability-gated, not assumed supported.
- Isolated worktree with explicit base/target branch, owner-controlled launch, reviewable PR and **no auto-merge**.
- DEV Operate and Production read-only grants are separate. Preserve least privilege, explicit environment binding, no ambiguous retry or cross-thread write.
- Distinguish source merged, frontend deployed, Convex/backend deployed, owner DEV-accepted and Production-accepted; each requires its own evidence.

## Open questions

- Is Convex DEV currently deployed at the matching v0.2 backend SHA, and are the exact native-write environment guards configured against a genuinely disposable T3 instance?
- Has the owner completed Helm's own Linear OAuth consent, mapping and live issue selection? No verified positive end-to-end evidence was available in this check.
- Which R01–R12 hosted receipts remain for EGA-685, including real PR verification and negative authorization/recovery tests?
- How should obsolete PRs #3–#5 and stale Linear tracking be reconciled without manufacturing acceptance?

## Next steps

1. Verify Convex DEV code/deployment parity, owner login and saved Linear OAuth connection; establish one safe mapped issue.
2. Independently verify a truly disposable T3 target, grant and DEV-only environment binding; obtain separate authorization before real launch/follow-up/interrupt.
3. Execute [EGA-685](https://linear.app/egawilldoit/issue/EGA-685) real R01–R12 DEV owner acceptance and record exact source/deployment identities, positive/negative cases and recovery.
4. Keep [EGA-768](https://linear.app/egawilldoit/issue/EGA-768) Production v0.2 **HOLD** until independent DEV acceptance and fresh explicit Production approval.

## Links

- [Helm GitHub](https://github.com/EGA-BUILDS/helm) · [DEV](https://dev-helm.egawilldoit.online) · [Production](https://live-helm.egawilldoit.online)
- [EGA-685](https://linear.app/egawilldoit/issue/EGA-685) · [EGA-768](https://linear.app/egawilldoit/issue/EGA-768)
- [Agent delivery workflow](../concepts/agent-delivery-workflow.md) · [Gymtrack](gymtrack.md)
