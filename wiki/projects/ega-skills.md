---
type: project
status: active
updated: 2026-11-03
related: [agent-delivery-workflow, 0005-ega-skills-automatic-production-release]
---

# EGA Skills

Governed skill registry and read-only MCP for coding agents. The 126-skill catalog, including nine TStack skills, is live. The MCP release flow is now automatic; separate Console and client acceptance remain open unless freshly demonstrated.

## Current state


### CI optimization shipped (2026-11-03)

Two GitHub PRs are now merged: [#197](https://github.com/egawilldoit/ega-skills/pull/197), Batch 1 config reuse/readiness diagnostics (merge `64f217b`), and [#198](https://github.com/egawilldoit/ega-skills/pull/198), Batches 2–5 concurrency and fail-closed Windows/Linux sharding (merge `10139052c`). [Post-merge `main` CI 41676267240](https://github.com/egawilldoit/ega-skills/actions/runs/41676267240) passed on that exact final SHA. The separate [live-release verifier 41676899300](https://github.com/egawilldoit/ega-skills/actions/runs/41676899300) was **IN PROGRESS (not certified)** at this record; the older [Batch 1 live verifier](https://github.com/egawilldoit/ega-skills/actions/runs/41624226642) failed because the Vercel deployment check remained pending. These are **not** CI-test failures and do not prove a canonical production promotion.

Measured historical Linux PR median **360 s** versus final-head **186 s** (~48% lower); historical full Linux/Windows median **1,056 s** versus five-run optimized median **489 s** (~54% lower). These are mixed-cohort descriptive comparisons, **not** compute-cost reductions; runner-minutes remain approximately neutral. Final sharded discovery was **190 files**, Windows **95+95 / 2,247 tests**, Linux core **76+76 / 1,451 tests**. Aggregators retain the exact release-required names and `ci-gate` rejects incomplete, failed or unexpectedly skipped shards. Two hosted negative-injection runs proved failure propagation. [Full 0–5 technical report](../../raw/ega-skills/2026-11-03-ci-cd-performance-optimization.md) contains all benchmarks, corrections, run URLs, residual risks and rollback.

### Production verified 2026-11-01

| Item | Verified state |
|---|---|
| Canonical service | [ega-skills-mcp.vercel.app](https://ega-skills-mcp.vercel.app) |
| Serving source | `main@1ddb2b6682ca8544467af9705218901325f1b7a8`, merged [PR #194](https://github.com/egawilldoit/ega-skills/pull/194) |
| Deployment | `dpl_DLpNWss77uBSHqo1VeU93vmmEhNL`; source `git`, production, READY, canonical alias attached |
| Readiness | [Canonical /readyz](https://ega-skills-mcp.vercel.app/readyz) returned HTTP 200 and `status: ready` |
| Catalog digest | `sha256:08b12c28b66a9705fbaf523131867046d9d0b47bafe42a8564daab58a7ed0266` |
| Main CI | [41330190910](https://github.com/egawilldoit/ega-skills/actions/runs/41330190910): success |
| Production verification | [41330714245](https://github.com/egawilldoit/ega-skills/actions/runs/41330714245): success, including authenticated live-release verification |
| Anonymous MCP | HTTP 401 `E_AUTH_REQUIRED`, checked after #194 |

Counts and digests describe this release, not permanent requirements. Future expected membership and versions come from the manifest committed with the release.

### Delivered scope

[PR #193](https://github.com/egawilldoit/ega-skills/pull/193), merged at `787f1f4b5d7affe8455c9035f174d7e6dec90686`, replaced the custom publisher with native Vercel Git delivery and shipped the 126 catalog. Its [production verification](https://github.com/egawilldoit/ega-skills/actions/runs/41327418845) passed. The supplied release report records all nine TStack versions retrieved through authenticated MCP inspection and L2 content: `babysit-pr`, `council`, `file-pr`, `full-send`, `full-stop`, `leaving-pr-comments`, `modernize`, `sol-loop`, and `takeover`, under `t3dotgg/`.

The reported first-release observation was decisive: Vercel reached READY while the old canonical deployment stayed live; domain assignment happened only after `ci-gate` succeeded. The #194 documentation merge then produced another automatic Git release, with main CI and production verification successful. Continued operation is therefore demonstrated by a second legitimate merge; it is not merely expected after the first release.

Work retained:
- Catalog/intake review, immutable versions, artifact validation, application authentication and authorization.
- GitHub CI and the required Vercel `GitHub CI gate` check for `ci-gate`.
- Automatic post-release verification and guarded recovery.
- Supabase control-plane bootstrap, where independently required for catalog access.

Work retired from the normal release path:
- `scripts/hosted/publish-production.mjs`, publisher-only helpers, publication exports and obsolete publisher tests.
- Separate candidate approval envelopes, expiring publication authorizations, per-release attestations/waivers and publication receipts.
- The intermediate 117 baseline migration, release-specific publisher worktrees, custom stage/promote/reconcile commands and GitHub-check fallback.
- Git deployment suppression and the ignored-build `exit 0` guard.

Historical cutover documents are superseded. Their old commands are not the current release procedure. Separate database-bootstrap receipts and intake review are not retired publisher machinery.

### Final release contract

Reviewed PR → merge to `main` → GitHub CI and native Vercel build run in parallel → both build and exact-SHA `ci-gate` succeed → Vercel assigns production domains → automatic live verification.

Code and catalog ship from the same commit. Merging is the release decision; agents still need authorization to merge. This project-specific release decision does not authorize automatic merging or production delivery in other EGA projects.

The project uses Production Branch `main` and Root Directory `packages/mcp`, with the pnpm workspace build. Git deployment must remain enabled, the Ignored Build Step empty, and automatic production domain assignment enabled. `gitProviderOptions.createDeployments: "enabled"` enables GitHub deployment-event delivery; it is not the Git auto-deploy permission.

Verification checks the canonical deployment, exact release digest, committed skill/version membership, authenticated retrieval, TStack L2 content and deployment-specific origin. A green PR, READY build or successful preview alone does not prove production delivery.

### Instructions for the next agent

1. Read the owning repository's [AGENTS.md](https://github.com/egawilldoit/ega-skills/blob/main/AGENTS.md) and [automatic release runbook](https://github.com/egawilldoit/ega-skills/blob/main/docs/operations/AUTOMATIC-RELEASE.md). Inspect fresh main, PR and canonical state; this page is a dated snapshot.
2. Make one bounded change. If skills change, regenerate and validate the catalog/artifact in that same PR. Derive expected identities from its committed manifest. Do not require the candidate digest to equal old production before release.
3. Run relevant checks and review exact-head CI. Normal main full CI uses Linux; Windows is selected only by the workflow's designated contexts. Do not invent an extra Windows release gate.
4. Before an authorized merge, retain the current known-good deployment and digest. Merge only after required PR checks pass. Let native Git delivery run; do not deploy or promote manually as a normal release.
5. Check main CI and Vercel at the merge SHA, then require canonical alias identity, readiness digest and the production-verification workflow to match. On a docs-only change, the catalog digest may stay unchanged while source/deployment identities change.
6. On failure, inspect the verifier result and follow the owning runbook. Configuration/authentication failure does not prove promotion never happened. Let recovery handle a failed live release; do not race it with another alias write.
7. Report source SHA, deployment identity, canonical readiness and verification-run evidence. Update Atlas with the new dated state; never publish credentials or raw logs.

### Recovery and evidence limits

The recovery helper restores a healthy predecessor only while the failed deployment remains canonical. It rechecks before writing and stops without mutation if superseded. It restores automatic domain assignment and deployment-event delivery; branch, required check and ignored-build configuration still need inspection after emergency recovery. Vercel alias assignment has no atomic compare-and-swap, so a residual race remains.

The supplied #193 report records focused adapter tests and a real superseded-release no-op without a production write. Destructive recovery was **not** demonstrated on a disposable live Vercel project. Do not describe tests as live recovery evidence or intentionally break canonical production to obtain it.

#194 aligned five guidance files with the implemented flow: agent instructions, automatic release runbook, operator runbook, MCP Vercel guide and the historical separate Web Console guide. Its web-console CI job initially timed out waiting for a test server; the unchanged commit passed on retry, followed by `ci-gate`. No runtime fix or gate bypass was used for that documentation merge.

## Decisions

- [0005 Automatic production release](../decisions/0005-ega-skills-automatic-production-release.md): use native Git builds and the platform CI gate; remove a second publisher state machine.
- Skills remain immutable and deterministically resolved. Existing project/release locks must not silently advance merely because hosted production releases automatically.
- MCP serves read-only skill content; selecting a skill does not execute its scripts.
- Keep authorization, control-plane access and artifact validation. Simpler delivery does not remove application boundaries.

## Open questions

- Latest `main@10139052c` CI succeeded; complete the separately tracked live production verification before treating this optimization merge as production release acceptance.
- Destructive recovery needs a disposable-project live demonstration before full platform recovery certification.
- Console owner/browser acceptance and hosted OpenCode acceptance were not re-audited by this MCP release. [EGA-731](https://linear.app/egawilldoit/issue/EGA-731) and [EGA-732](https://linear.app/egawilldoit/issue/EGA-732) had unresolved acceptance in the older Atlas snapshot; check current issue state before acting.
- The earlier published v2.0.0 tag versus package 2.0.1 discrepancy was not re-audited here. A hosted catalog digest is separate from package/tag release status.

## Next steps

1. Recheck production-verification run `41676899300` and canonical deployment for `main@10139052c`; reconcile the older pending-check failure.
2. Investigate the pre-existing `ega-719` HTTPS mock/timeout flake; keep the 403 assertion intact.
3. Validate release-target `release/2.0` status checks against the new aggregators before a release promotion.
4. Use the automatic path for the next authorized change; retain its source, canonical and verifier evidence.
5. When recovery certification becomes the active task, demonstrate failure/recovery in a disposable Vercel project.
6. Recheck EGA-731/EGA-732 and package/tag status before claiming complete Console/client acceptance.

## Links

- [CI optimization, full Batch 0–5 Atlas record](../../raw/ega-skills/2026-11-03-ci-cd-performance-optimization.md)
- [Merged Batch 1 PR #197](https://github.com/egawilldoit/ega-skills/pull/197) and [Batch 2–5 PR #198](https://github.com/egawilldoit/ega-skills/pull/198)
- [Merged-sha main CI](https://github.com/egawilldoit/ega-skills/actions/runs/41676267240)
- [Automatic release: operating source of truth](https://github.com/egawilldoit/ega-skills/blob/main/docs/operations/AUTOMATIC-RELEASE.md)
- [Operator runbook](https://github.com/egawilldoit/ega-skills/blob/main/docs/operations/RELEASE-2.0-RUNBOOK.md)
- [MCP Vercel configuration](https://github.com/egawilldoit/ega-skills/blob/main/packages/mcp/VERCEL.md)
- [Verification workflow](https://github.com/egawilldoit/ega-skills/blob/main/.github/workflows/verify-release.yml)
- [Catalog intake and publication](https://github.com/egawilldoit/ega-skills/blob/main/docs/INTAKE-TO-PUBLICATION.md)
- [Release simplification PR #193](https://github.com/egawilldoit/ega-skills/pull/193)
- [Agent/runbook alignment PR #194](https://github.com/egawilldoit/ega-skills/pull/194)
- [Agent delivery workflow](../concepts/agent-delivery-workflow.md)
