---
type: decision
status: accepted
updated: 2026-11-01
related: [ega-skills, agent-delivery-workflow]
---

# 0005 EGA Skills automatic production release

Release reviewed code and catalog together from main through native Vercel Git delivery, gated by CI and followed by live verification.

## Context

The owner chose a simple release: merge an approved PR, let main CI pass, deploy automatically, verify the committed skills, recover if verification fails. The custom governed publisher delayed a valid catalog behind a separate 117-baseline cutover, approval envelopes, attestations, CLI deployment handling and promotion bookkeeping.

The supplied work reports describe mismatches between that publisher and the platform: file-based Git settings absent from Project API readback; CLI builds needing workspace/prebuilt handling; CLI deployment URLs parsed incorrectly; no checks attached to the observed CLI deployments; and an `externalChecks` scope error despite green tests. These are reported causes, not a fresh audit of every historical attempt. They exposed a release route that tests had not exercised end to end.

## Options

1. Keep repairing the custom publisher and preserve its baseline migration and separate approvals.
2. Add a separate checks integration for CLI deployments.
3. Use the existing native Vercel Git build, required GitHub CI gate, automatic domain assignment and a small verification/recovery workflow.

## Decision

Choose option 3 for the hosted MCP project. The owner also chose that code and the reviewed catalog ship from the same main commit. [PR #193](https://github.com/egawilldoit/ega-skills/pull/193) implements this; [PR #194](https://github.com/egawilldoit/ega-skills/pull/194) aligns agent guidance and runbooks.

GitHub CI and Vercel builds may run in parallel. Production domains wait for the build and exact-commit `ci-gate`. A successful deployment event starts verification of canonical identity, committed membership and authenticated content.

Retire the publisher-only cutover, envelopes, attestations, waiver/receipt state and manual stage/promote route. Preserve intake review, immutable versions, validation, authentication, authorization and independently necessary control-plane bootstrap.

## Consequences

- Merging an authorized, reviewed PR is the release action. Manual production deployment/promotion is outside the normal process; guarded emergency recovery remains supported.
- Counts/digests come from each committed artifact. READY is not proof of live delivery; canonical and verification evidence are required.
- Native release succeeded for #193 and the next legitimate merge #194. Main CI and production verification passed for #194, closing the initial second-release observation gap.
- Recovery cannot atomically compare-and-swap aliases. It rechecks, refuses to overwrite a newer canonical deployment and restores automatic assignment/event delivery.
- Destructive recovery still lacks a disposable-project live demonstration.
- This is an EGA Skills delivery choice, not permission for agents to auto-merge or change other projects' production policies.

## Links

- [Current project state and agent handoff](../projects/ega-skills.md)
- [Authoritative operating procedure](https://github.com/egawilldoit/ega-skills/blob/main/docs/operations/AUTOMATIC-RELEASE.md)
- [Implementation #193](https://github.com/egawilldoit/ega-skills/pull/193)
- [Documentation #194](https://github.com/egawilldoit/ega-skills/pull/194)
- [Main CI evidence](https://github.com/egawilldoit/ega-skills/actions/runs/41330190910)
- [Production verification evidence](https://github.com/egawilldoit/ega-skills/actions/runs/41330714245)
