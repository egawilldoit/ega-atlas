# Gymtrack — CircleCI performance optimization delivery report

**Date:** 2026-11-02  
**Status:** Implemented and merged into `develop`. Protected-branch validation succeeded on the merge commit.  
**Scope:** Gymtrack application CI/CD only. This is not a Sprint 2 feature release or a production-readiness certificate.  
**Code owner:** [trackgym101/gymsas](https://gitlab.com/trackgym101/gymsas) on GitLab  
**Change:** [MR !124 — ci: parallelize CircleCI validation and optimize runtime](https://gitlab.com/trackgym101/gymsas/-/merge_requests/124)  
**Source branch:** `docs/ci-performance-audit`  
**Source head:** `6e6055d095f0ac0a420d10388bf3299125ad6a52`  
**Merged into:** `develop` on 2026-11-02 at 20:12:40 UTC  
**Merge commit:** [`a23dab522060be2fd0547952e84f85bbb34ab6e3`](https://gitlab.com/trackgym101/gymsas/-/commit/a23dab522060be2fd0547952e84f85bbb34ab6e3)

> Evidence boundary: the benchmark, tests, cost estimates and implementation details below are from MR !124's code, audit documents, local/CI completion reports and GitLab results. GitLab independently reports a successful protected-branch CircleCI status on the merge SHA. This Atlas writing pass did not independently retrieve every CircleCI job log or the account's final billed storage export. A green CI status does not certify the hosted Gymtrack user journey or authorize Production.

## 1. Outcome

Gymtrack's CircleCI workflow was slow because the web checks, API unit checks, API HTTP E2E checks and delivery checks ran serially inside one `application-validation` job. The database and candidate catalog already ran separately, so application validation determined the successful workflow's wall time.

MR !124 split the application checks into four parallel CircleCI jobs, retained the existing isolated PostgreSQL acceptance, hardened exact-job-set evidence, adjusted pnpm cache retention/invalidation and tested a higher database executor class. The higher executor class did not help and was reverted. A final security fix removed a caller-controlled required-job override from the evidence helper.

**Observed result:** the historical successful workflow median was **328.97 seconds**. The last pre-merge feature-branch workflow, pipeline **#326**, completed in **205.4 seconds**, about **37.6% less elapsed time than that historical median**. This compares a single recent run with an older median; it is **not** a confirmed new p50. Other completed post-split observations were about **205–236 seconds**, showing database-suite variability.

**Merge result:** MR !124 is merged. The `develop` merge SHA subsequently received **success** for `CircleCI: Workflow protected-branch-validation`. Vercel Web and API checks also reported success for that SHA, but those checks are separate from application acceptance.

The project did **not** migrate CI providers, activate GitLab-native application CI, change database policy, approve a release or buy a paid runner.

## 2. Before the change: topology and bottleneck

The existing CI was CircleCI, driven from the GitLab repository. Two workflow variants mattered:

- `pilot-validation` runs for the feature/validation path and includes independent `candidate-catalog` generation.
- `protected-branch-validation` runs on the protected integration branches and excludes `candidate-catalog`.

The pre-change critical jobs were:

| Job | Role | Historical measured duration |
| --- | --- | ---: |
| `application-validation` | Serial `pnpm check` plus migration-history checks | 316.65s median across 20 sampled executions |
| `database-validation` | Disposable loopback PostgreSQL, migrations, RLS and domain acceptance | 213.95s median |
| `candidate-catalog` | Separate candidate catalog generation for pilot workflow | 61.26s median |

`pnpm check` took **271.49s median (n=19)** and sequentially exercised contracts, web, API and delivery. The application median of **316.65s** must not be confused with **326s**, which was one specific pipeline, #318.

The principal opportunity was to overlap independent suites on separate runners, not to remove tests, skip database acceptance or change production infrastructure.

### Baseline cohort and resources

The audit sampled pipelines **#299–#318**, with **17 successful** and **3 failed** pipelines. Successful workflow wall-clock **p50 = 328.97s** and **p90 = 349.67s**. The application job was nearly CPU-saturated on its small executor; resource observations included **99% CPU**, **47% median RAM** and **98% peak RAM**. The peak prevents calling memory universally underutilized.

A separate 30-day provider usage window reported **310 pipelines**, **750 unique jobs** and **12,647 credits**: **10,437 compute** and **2,210 storage**. These are different sampling windows. The storage figure includes multiple object classes, not just pnpm cache. Over 39 recent completed workflows the observed arithmetic was **46.87 compute + 15.51 storage = 62.38 credits/workflow**.

A conditional **313 workflows/month** planning rate yielded **~19,526 credits/month** for the old configuration. That multiplier was an assumption, not a count of incoming future workflows.

## 3. Independent review and corrections

The original audit covered **43 optimization techniques**. An independent model review and a separate adjudication corrected the implementation plan before code changes.

### Safety findings

| Finding | Consequence | Decision |
| --- | --- | --- |
| CircleCI may ignore dependencies that have been filtered out of `requires` | A green no-op gate alone cannot prove complete validation | Keep mandatory jobs unfiltered. Independently verify each canonical job once with terminal success. |
| Global auto-cancel would apply to `develop` because the provider default branch is `main` | Validation evidence for a merged `develop` SHA could be canceled by a later push | Leave global auto-cancel disabled. |
| A prefix named `protected` is only part of a cache key, not an access-control boundary | Cross-branch sharing can admit an untrusted writer | Preserve branch identity in protected keys; ultimately retain branch scoping in both scopes. No broad fallback. |
| The proposed `small` to `medium` upgrade had no matched performance evidence | Doubling credits/minute might not reduce wall time | Split first. Benchmark only the critical job, then keep or revert on evidence. |
| `validation-gate` was proposed as the sole proof | Dependency orchestration is weaker than explicit evidence validation | Add workflow-specific complete-job assertions; reject gate-only evidence. |

CircleCI documents [filtered dependency semantics](https://circleci.com/docs/reference/configuration-reference/#requires) and [immutable caches, first-writer behavior and job-level retention](https://circleci.com/docs/guides/optimize/caching/).

### Audit corrections retained in the record

- `pnpm check` and isolated-acceptance step durations use **n=19**; one job failed before those steps.
- The acceptance-step median was **160.20s**, not a blanket 20-run result.
- The original critical-path breakdown counted some residual work twice. Known measured subphases and residual are now disjoint.
- Whole-run overlapping cancellation time was not recoverable savings. The revised recoverable-work estimate was roughly **107 / 3,738 credits (~2.9%)**, before real cancellation latency.
- Reader jobs gain less from cache hits than the writer: estimated **~2–5s** for readers versus **~8–12s** for the writer on comparable misses.
- Both web and API TypeScript configurations already enable incremental compilation. Cross-job `.tsbuildinfo` reuse, not incremental support itself, remained unproven.
- The original plan mistakenly discussed removing build-before-test ordering. Tests already preceded builds. The actual waste was **serial web/API/delivery execution**.
- The corrected 43-technique statuses were: **15 not applicable, 7 missing, 12 improve, 4 already implemented, 4 needs evidence, 1 unsafe**.

The review changed the order of operations: **correct evidence → split application jobs → correct cache policy → measure the database executor → stop when further work lacks a safe cost/critical-path case**.

## 4. Batch 0 — reproducible evidence and CI contracts

**Primary files:** [`scripts/ci/circleci-evidence.mjs`](https://gitlab.com/trackgym101/gymsas/-/blob/develop/scripts/ci/circleci-evidence.mjs), its tests, [`scripts/tests/ci-config.test.mjs`](https://gitlab.com/trackgym101/gymsas/-/blob/develop/scripts/tests/ci-config.test.mjs), and the two [audit documents](https://gitlab.com/trackgym101/gymsas/-/tree/develop/docs/ci).

The evidence helper has canonical, workflow-specific required-job contracts:

| Workflow | Mandatory jobs |
| --- | --- |
| `pilot-validation` | `web-validation`, `api-unit-validation`, `api-e2e-validation`, `delivery-validation`, `database-validation`, `candidate-catalog`, `validation-gate` |
| `protected-branch-validation` | `web-validation`, `api-unit-validation`, `api-e2e-validation`, `delivery-validation`, `database-validation`, `validation-gate` |

Evidence fails closed for a wrong SHA or ref, unknown workflow, wrong project/pipeline identity, absent job, unexpected extra job, duplicate job, failure, cancellation, skip, incomplete status, cross-pipeline mix and stale independent approval.

The final pre-merge review found a bypass: callers could override `requiredJobs` with a reduced list, such as only `validation-gate`. Commit [`6e6055d`](https://gitlab.com/trackgym101/gymsas/-/commit/6e6055d095f0ac0a420d10388bf3299125ad6a52) removed that caller-controlled input. `assertCircleCiEvidence()` now derives the job set internally from `requiredJobsForWorkflow(expectedWorkflow)`. A direct gate-only override regression verifies that the incomplete set still fails.

**Final local evidence:** **18/18** evidence-helper tests, **14/14** CircleCI config tests and **279** delivery tests (**254 passed, 25 optional skipped, zero failed**). Earlier Batch 0 validation counted **16/16** evidence tests; two more were added by the final hardening.

The no-op gate remains a useful job dependency, but **not an independent certificate** that every mandatory job executed. The evidence-helper contract is tested; the GitLab required external status does not itself call that helper during merge evaluation.

## 5. Batch 1 — parallel application validation

**Main config:** [`.circleci/config.yml`](https://gitlab.com/trackgym101/gymsas/-/blob/develop/.circleci/config.yml).

The old `application-validation` became four independent small-executor jobs:

```text
web-validation ───────────────┐
api-unit-validation ──────────┤
api-e2e-validation ───────────┼── validation-gate
delivery-validation ──────────┤
database-validation ──────────┘

candidate-catalog              (pilot only; independent)
```

| Job | Validation retained |
| --- | --- |
| `web-validation` | Web lint, TypeScript, web tests and production build |
| `api-unit-validation` | API lint, TypeScript, unit tests and NestJS build |
| `api-e2e-validation` | API HTTP end-to-end tests |
| `delivery-validation` | Contracts, delivery/CI-script checks and migration-history verification against the integration branches |
| `database-validation` | Existing isolated PostgreSQL migration, RLS and database acceptance suites |
| `candidate-catalog` | Pilot-only candidate generation with separate required-success evidence |
| `validation-gate` | No-op orchestration requiring the five mandatory validation jobs |

Each application job checks out the exact commit and uses the frozen install. Separating containers avoids shared build-output and port contention. The workflow names stayed `pilot-validation` and `protected-branch-validation`. Mandatory jobs were kept unfiltered.

**Test parity recorded during delivery:**

| Suite | Retained coverage |
| --- | --- |
| Web | **756 tests**, lint, TypeScript and production build |
| API unit | **635 tests**, lint, TypeScript and build |
| API HTTP E2E | **135 tests** |
| Database | **474 Vitest tests + 5 Node tests**, local isolated PostgreSQL |
| Delivery | Contract, delivery/script and migration-history tests; final local command: **279 total, 254 passed, 25 optional skips** |

A previous delivery-suite receipt reported **277 tests, 252 passed, 25 skipped**. The final receipt increased by two passing tests. Do not confuse local delivery command totals with a single historical CircleCI log.

The first post-split pilot workflow, **#319**, succeeded in **219.6s**. This established the structural gain without removing the expensive isolated database tests. The database path became the workflow's limiting job.

## 6. Batch 2 — conservative pnpm cache policy

**Goal:** avoid unnecessary storage churn while preserving repeatable installs and cache trust. This was not a claim that caching would cut the critical path.

Implemented:
- Keep pnpm store at `/tmp/pnpm-store` and perform frozen installation.
- Include `pnpm-workspace.yaml` in cache invalidation because install policy such as `allowBuilds` can change the result.
- Save cache only from `web-validation`; other jobs restore without writing.
- Add **7-day** job-level cache retention to the writer.
- Retain protected and untrusted cache scopes, **including `.Branch` in both keys**.
- Do not add a broad cache fallback, cached `node_modules` or generated outputs.
- Do not enable cross-branch sharing on the basis of a name prefix.

A recorded new cache object was approximately **175 MiB**. The earlier **1,895 storage credits** were the application-job storage component, not a verified pnpm-only bill. The true savings from shorter retention remain **unmeasured until the later billed-usage export**. A cache hit also does not establish that wall time improves: retrieval, install, save and parallel read races all matter.

CircleCI [caches are immutable](https://circleci.com/docs/guides/optimize/caching/). Changing an exact key creates a new object; it does not immediately remove older objects.

## 7. Batch 3 — higher database executor tested and rejected

After the split, the longest job was `database-validation`. The team therefore tested `medium` as a bounded experiment instead of upgrading all jobs.

| Metric | Medium test | Small-executor comparisons |
| --- | ---: | --- |
| Workflow wall time | **223.4s** | Approximately **216.2s, 226.3s, 224.0s** |
| Database job | **220.6s** | Approximately **216–226s** |
| Database acceptance step | **181.8s** | **165.5s, 165.1s** |
| Nominal compute rate | **2× small** | **1× small** |

The medium class did **not** shorten the critical path and could double the compute credit rate. The configuration was reverted to **small**.

This was not a perfect matched-SHA experiment because an executor change changes the configuration commit. No two-core speedup is claimed. The decision rests on observed lack of improvement, not a guarantee that future workload conditions will be identical.

## 8. Final checks, merge and post-merge validation

### Pre-merge evidence

The final pre-merge source commit was `6e6055d095f0ac0a420d10388bf3299125ad6a52`.

| Workflow | SHA/scope | Result |
| --- | --- | --- |
| Pilot #325 | Previous source head `0979b6f` | **Success**, **215.3s** |
| Pilot #326 | Final source head `6e6055d` | **Success**, **205.4s** |
| MR head external status | Same `6e6055d` | **Success**; GitLab MR mergeable |
| Protected-branch validation | Merge commit `a23dab5` on `develop` | **Success**, independently observed from GitLab commit status |

Both pilot workflows #325 and #326 were reported as containing each of the **seven canonical jobs once**, all successful. The candidate catalog had its own successful job. The linked [final pilot workflow's source commit](https://gitlab.com/trackgym101/gymsas/-/commit/6e6055d095f0ac0a420d10388bf3299125ad6a52) contains the final evidence-helper fix.

The owner authorized the merge. [MR !124](https://gitlab.com/trackgym101/gymsas/-/merge_requests/124) merged into `develop`, producing `a23dab522060be2fd0547952e84f85bbb34ab6e3`.

Post-merge, GitLab reported **success** for:
- [CircleCI `protected-branch-validation`](https://app.circleci.com/workflow/792a3dc9-6b7b-4c34-96bc-5b768af5ee32), tied to the exact merge SHA.
- Vercel `gymsas-web` and `gymsas-api` checks, tied to the merge SHA.

These successes establish **merged code and protected CI**, not manual browser acceptance or Production authorization. This documentation update did not trigger a release or deployment; provider integrations may create automatic deployments and status checks.

### Implementation commit trail

The MR kept its batch commits. Key receipts:

| Stage | Commit | Result |
| --- | --- | --- |
| Audit correction and canonical evidence | `1d51a9b` | Baseline corrected; strict workflow contracts introduced |
| Four-way application split | `a2b5330` | First passing pilot workflow #319 |
| Cache policy | `747fc85` | Workspace install-policy key; 7-day writer retention |
| Medium experiment and revert | `d9c24a6`, `e8cfea1` | No speed benefit; restored small |
| Final audit/performance record | `e518c2e`, `cd5a257`, `0979b6f` | Results, risks and deferrals written |
| Gate-only evidence bypass fix | `6e6055d` | Caller-supplied `requiredJobs` removed |
| Integration merge | `a23dab5` | Merged into `develop`, protected CircleCI success |

Use the [MR commits tab](https://gitlab.com/trackgym101/gymsas/-/merge_requests/124/commits) for the full canonical ordering and complete commit SHAs.

## 9. Performance results and what they mean

| Measure | Before | After / current | Evidence quality |
| --- | ---: | ---: | --- |
| Successful workflow wall time | **328.97s p50** | **205.4s** latest #326 | Historical median vs one verified recent run |
| Historical p90 | **349.67s** | Not established with a comparable large sample | Avoid a new statistical p90 claim |
| Recent post-split workflow wall | — | Approximately **205–236s** | Small sample, variable database runtime |
| Critical path | `application-validation` | `database-validation`, roughly **203–233s** | Observed structural bottleneck shift |
| Latest database job | **213.95s** historical median | **202.6s** on #326 | Different measurement windows |
| Compute/workflow | **46.87 credits** historical mean | **~57–61 modeled credits** | Compute increased with duplicated setup |
| Total monthly spend | **~19.5k modeled credits** | **~19–20.5k modeled credits** | Neither is final billed post-change usage |
| Cache lifetime | Prior policy | **7 days** on single writer | Verified configuration, not verified financial savings |

The latest-run improvement against the historical median is:

```text
(328.97 - 205.4) / 328.97 × 100 = 37.56%
```

For an approximately 215-second run, the same comparison yields about **34.6%**. Across the observed post-split range, the comparison is about **28–38%**. These are **descriptive single-run changes**, not a controlled new p50/p90 and not a guaranteed future gain.

**Trade-off:** four independent application jobs incur more repeated checkout, install and setup. The user gets much shorter feedback, but aggregate compute credits can rise even when workflow wall time falls. The intended budget guardrail was **24,000 project credits/month**, subject to the account's actual available Free-plan allowance. The billed storage and monthly forecast need later confirmation.

## 10. What did not change

This MR was deliberately a CI orchestration and evidence change, not a product feature release.

- No changes to Gymtrack's Owner/Manager custom RBAC behavior, Member/Plan/Membership/Finance authorization model or frontend product features.
- No changes to RLS policy, tenant/site isolation or the shared DEV migration delivery protocol.
- No need for Docker or a local PostgreSQL installation on the T3 Code host.
- Database acceptance still uses disposable local clusters inside CI; shared DEV/PROD is not the test target.
- CircleCI remains active application CI; native GitLab CI was not activated.
- No migration to Blacksmith or self-hosted runners.
- No global CircleCI auto-cancel, because `develop` exact-SHA validation must remain available.
- No automatic approval to deploy to Production.

The independent catalog job remains an independently required success for the pilot workflow. The protected workflow intentionally omits it.

## 11. Deferred work, decisions and triggers to reopen

| Item | Current decision | Reopen when |
| --- | --- | --- |
| 7-day credit/storage evaluation | **Pending** | A provider billed-usage export covers enough days after retention change to compare cache and artifact storage honestly |
| New successful-workflow p50/p90 | **Pending** | At least 10 comparable, completed, scope-matched post-change runs have accumulated |
| Duplicate TypeScript/build passes | **Deferred** | The application job becomes critical or a measured credit-only benefit exceeds implementation risk |
| Two-way database sharding | **Not approved** | Owner explicitly accepts complexity and full shard test/isolation evidence plus updated budget model |
| Database `medium` executor | **Rejected for now** | A materially changed database workload warrants a new benchmark |
| Global auto-cancel | **Off** | A scoped policy is reviewed that cannot discard required `develop` validation evidence |
| Path-based conditional execution | **Deferred** | Dynamic configuration and exact-required-status coverage can be certified, including omitted and filtered jobs |
| Protected-branch workflow first live run | **Completed** | GitLab already reports success on the exact `a23dab5` merge commit |

The optional database-sharding model suggested approximately **150 seconds** and **~7–8 additional compute credits/workflow**. It is **not delivered, budget-certified or promised**. It requires updating `scripts/db/test-isolated.sh` to accept a shard selector, run the standalone customer test once, isolate each PostgreSQL cluster and prove the full test-file union, including RLS and finance cases.

Do not reopen the entire 43-point audit to optimize a noncritical step. First gather a stable post-change latency cohort and the actual bill.

## 12. Practical handoff

**Present state as of 2026-11-02:** MR !124 is merged into `develop`, exact-merge-SHA protected CircleCI is successful and CI runtime is materially lower in the observed post-split runs. Application functionality and hosted acceptance remain separate release gates.

**Next evidence tasks:** (1) export real post-change CircleCI storage/compute credits after the seven-day cache retention window; (2) capture ten comparable successful workflows to calculate a defensible new p50/p90; (3) continue Sprint 3's existing hosted security and deletion-policy work separately from this completed CI initiative.

**Sources of truth:**
- [Gymtrack MR !124, review and commits](https://gitlab.com/trackgym101/gymsas/-/merge_requests/124)
- [CircleCI implementation](https://gitlab.com/trackgym101/gymsas/-/blob/develop/.circleci/config.yml)
- [CI performance audit](https://gitlab.com/trackgym101/gymsas/-/blob/develop/docs/ci/performance-audit.md)
- [Optimization and rollback plan](https://gitlab.com/trackgym101/gymsas/-/blob/develop/docs/ci/optimization-plan.md)
- [Canonical evidence helper](https://gitlab.com/trackgym101/gymsas/-/blob/develop/scripts/ci/circleci-evidence.mjs)
- [CircleCI protected-branch post-merge workflow](https://app.circleci.com/workflow/792a3dc9-6b7b-4c34-96bc-5b768af5ee32)
- [CircleCI filtered dependencies](https://circleci.com/docs/reference/configuration-reference/#requires)
- [CircleCI cache semantics and retention](https://circleci.com/docs/guides/optimize/caching/)
