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

> Evidence boundary: this is an Atlas-authored summary of MR !124, the project-owned audit/plan, local/CI completion reports and GitLab results. The GitLab API independently confirms the merge SHA and a successful `protected-branch-validation` external status. **It does not expose the six protected CircleCI job outcomes or the protected workflow's duration; those have not been individually rechecked here.** The seven pilot job outcomes for #325/#326 come from the implementation agent's CircleCI API receipts, not a separate Atlas job-by-job retrieval. No final billed storage export or independently sampled production user journey is available. Green CI is not Production authorization.

## 1. Outcome

Gymtrack's CircleCI workflow was slow because the web checks, API unit checks, API HTTP E2E checks and delivery checks ran serially inside one `application-validation` job. The database and candidate catalog already ran separately, so application validation determined the successful workflow's wall time.

MR !124 split the application checks into four parallel CircleCI jobs, retained the existing isolated PostgreSQL acceptance, hardened exact-job-set evidence, adjusted pnpm cache retention/invalidation and tested a higher database executor class. The higher executor class did not help and was reverted. A final security fix removed a caller-controlled required-job override from the evidence helper.

**Observed result:** the historical successful workflow median was **328.97 seconds** across **17 successful runs**. The last pre-merge pilot, **#326**, took **205.4 seconds**, or **37.6% less** than that historical median. Seven reported successful post-split **small-executor** pilot runs (#319, #320 and #322–#326) have a **provisional median of 216.8 seconds** and a **nearest-rank p90 of 235.7 seconds**. That is approximately **34.1% lower** than the historical median, but these are only seven near-term runs across several commits/cache states: they do **not** establish a stable new long-run p50/p90 or an exact-SHA A/B.

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

The original audit covered **43 optimization techniques** adapted from the earlier T3 Code CI-performance exercise. The [complete item-by-item transfer matrix](#12-the-43-t3-code-derived-ci-optimization-techniques--gymtrack-outcomes) distinguishes concepts reused from changes actually shipped. An independent model review and a separate adjudication corrected the implementation plan before code changes. The source [audit](https://gitlab.com/trackgym101/gymsas/-/blob/a23dab522060be2fd0547952e84f85bbb34ab6e3/docs/ci/performance-audit.md) still opens with an audit-time **“proposed”** status, and the source [optimization plan](https://gitlab.com/trackgym101/gymsas/-/blob/a23dab522060be2fd0547952e84f85bbb34ab6e3/docs/ci/optimization-plan.md) ends with **“ready for owner review, not merge.”** Those sentences are stale historical drafting states at the merged SHA; this report and the GitLab MR/commit statuses provide the later outcome. Updating those private-repository headings was **not** part of this Atlas-only correction.

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

The no-op gate remains a useful job dependency, but **not an independent certificate** that every mandatory job executed. `scripts/ci/circleci-evidence.mjs` is a tested validator for evidence supplied to it; **neither the live CircleCI workflow nor GitLab's external-status merge check automatically invokes it for a fresh API job-set audit**. Current CI configuration tests parse the job list and reject mandatory job filters, and GitLab requires a green external workflow status. Those safeguards support the shipped graph, but they must not be described as runtime enforcement of the helper's canonical job-set contract. Before any release that relies on exact completed-job evidence, retrieve and validate the live protected workflow's actual jobs, not only its aggregate status.

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

Each application job checks out the exact commit and uses the frozen install. Separating containers avoids shared build-output and port contention. The workflow names stayed `pilot-validation` and `protected-branch-validation`. Mandatory jobs were kept unfiltered. **Important distinction:** the pilot `validation-gate` depends on the five validation jobs but **does not depend on `candidate-catalog`**. The catalog remains a separate pilot workflow job; the canonical seven-job evidence contract requires its success whenever independently evaluated. CircleCI's `requires` semantics alone do not prove that a filtered or omitted catalog job executed.

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

### Recorded pilot run sequence

These are agent-reported CircleCI durations on successive feature-branch commits. Only the seven `small` observations are grouped for the preliminary median; the intentionally tested `medium` run is excluded.

| Pilot pipeline | Resource experiment | Reported workflow wall time | Interpretation |
| --- | --- | ---: | --- |
| #319 | Split; all `small`, first run | **219.6s** | First successful split validation |
| #320 | Split; all `small`, subsequent run | **229.1s** | Cache-policy follow-up, no guaranteed cache speedup |
| #321 | Database `medium` | **223.4s** | Benchmark, **excluded** from small-only sample; reverted |
| #322 | All `small` | **216.8s** | Database returned to critical path |
| #323 | All `small` | **235.7s** | Slowest recorded small run of this sequence |
| #324 | All `small` | **205.5s** | Faster database execution |
| #325 | All `small` | **215.3s** | Previous head; seven jobs reported successful |
| #326 | All `small`, final pre-merge head | **205.4s** | Seven jobs reported successful; SHA `6e6055d` |

**Calculation:** sorted small-run durations are `205.4, 205.5, 215.3, 216.8, 219.6, 229.1, 235.7` seconds. Their midpoint median is **216.8s (n=7)** and nearest-rank p90 is **235.7s (n=7)**. Neither should be reported as a robust, 10-run, scope-matched performance certification; configuration and cache inputs changed across this series.

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

Both pilot workflows #325 and #326 were reported by the implementation agent's CircleCI API check as containing each of the **seven canonical jobs once**, all successful. The candidate catalog had its own successful job. GitLab independently confirms that the final source head had a successful external CircleCI status. The linked [final pilot workflow's source commit](https://gitlab.com/trackgym101/gymsas/-/commit/6e6055d095f0ac0a420d10388bf3299125ad6a52) contains the final evidence-helper fix.

The owner authorized the merge. [MR !124](https://gitlab.com/trackgym101/gymsas/-/merge_requests/124) merged into `develop`, producing `a23dab522060be2fd0547952e84f85bbb34ab6e3`.

Post-merge, GitLab reported **success** for:
- [CircleCI `protected-branch-validation`](https://app.circleci.com/workflow/792a3dc9-6b7b-4c34-96bc-5b768af5ee32), tied to the exact merge SHA.
- Vercel `gymsas-web` and `gymsas-api` checks, tied to the merge SHA.

These statuses establish **merged code, successful aggregate protected CI, and successful Vercel checks**, not a job-by-job re-audit of protected CircleCI, manual browser acceptance or Production authorization. Automatic Git-backed Vercel deployments can occur alongside those checks; **“no deployment” in the MR implementation report means no manual application release/deployment was initiated by the CI change**, not that Vercel performed no preview or branch builds. This Atlas documentation update did not trigger a release or deployment. No measured post-merge protected workflow runtime is available here.

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
| Successful workflow wall time | **328.97s p50 (n=17)** | **216.8s preliminary small-only median (n=7)**; #326 **205.4s** | Different cohorts; new median is provisional, not a stable p50 |
| Successful workflow p90 | **349.67s (n=17)** | **235.7s provisional nearest-rank p90 (n=7)** | Small sample; not an established long-run p90 |
| Recent post-split workflow wall | — | Approximately **205–236s** | Small sample, variable database runtime |
| Critical path | `application-validation` | `database-validation`, roughly **203–233s** | Observed structural bottleneck shift |
| Latest database job | **213.95s** historical median | **202.6s** on #326 | Different measurement windows |
| Compute/workflow | **46.87 credits** historical mean | **~57–61 modeled credits** | Compute increased with duplicated setup |
| Total monthly spend | **~19.5k modeled credits** | **~19–20.5k modeled credits** | Neither is final billed post-change usage |
| Cache lifetime | Prior policy | **7 days** on single writer | Verified configuration, not verified financial savings |

The latest-run improvement against the historical median is:

```text
(328.97 - 205.4) / 328.97 × 100 = 37.56%
(328.97 - 216.8) / 328.97 × 100 = 34.10%  (provisional n=7 median)
```

For an approximately 215-second run, the same comparison is about **34.6%**. Across the observed post-split range, the single-run comparisons are about **28–38%**. The n=7 median provides a better descriptive center than the fastest final run, but commit/cache variation and the short window prevent claiming a stable p50/p90 or guaranteed future gain.

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
| Stable new successful-workflow p50/p90 | **Pending** | Current small-executor sequence has a **provisional n=7 median of 216.8s and p90 of 235.7s**; collect at least 10 comparable, completed, scope-matched runs with cache/config cohort labels |
| Duplicate TypeScript/build passes | **Deferred** | The application job becomes critical or a measured credit-only benefit exceeds implementation risk |
| Two-way database sharding | **Not approved** | Owner explicitly accepts complexity and full shard test/isolation evidence plus updated budget model |
| Database `medium` executor | **Rejected for now** | A materially changed database workload warrants a new benchmark |
| Global auto-cancel | **Off** | A scoped policy is reviewed that cannot discard required `develop` validation evidence |
| Path-based conditional execution | **Deferred** | Dynamic configuration and exact-required-status coverage can be certified, including omitted and filtered jobs |
| Protected-branch workflow first live run | **Completed at aggregate-status level** | GitLab reports success on the exact `a23dab5` merge commit; actual six-job completeness and post-merge duration were not independently retrieved here |
| Protected live job evidence for release | **Not independently verified here** | Retrieve all six actual `protected-branch-validation` jobs for the exact merge/release SHA and run the canonical evidence check before any release decision |
| Historical source audit/plan status wording | **Stale in private repository** | These files still describe pre-merge states; MR !124 and this dated Atlas report supersede those statements. Correct in a separate documentation-only GitLab change if desired |

The optional database-sharding model suggested approximately **150 seconds** and **~7–8 additional compute credits/workflow**. It is **not delivered, budget-certified or promised**. It requires updating `scripts/db/test-isolated.sh` to accept a shard selector, run the standalone customer test once, isolate each PostgreSQL cluster and prove the full test-file union, including RLS and finance cases.

Do not reopen the entire 43-point audit to optimize a noncritical step. First gather a stable post-change latency cohort and the actual bill.

## 12. The 43 T3 Code-derived CI optimization techniques — Gymtrack outcomes

### Provenance and scope

The optimization approach was **transferred from the earlier T3 Code CI-performance exercise**: a checklist of 43 techniques, not an imported T3 Code script or a copy of its GitHub Actions workflow. Each technique was evaluated independently against Gymtrack's **GitLab + CircleCI**, Linux-only, isolated-PostgreSQL architecture. The [43-row source audit](https://gitlab.com/trackgym101/gymsas/-/blob/a23dab522060be2fd0547952e84f85bbb34ab6e3/docs/ci/performance-audit.md#L329-408) establishes the *pre-implementation* status. The table below adds what **MR !124 actually delivered**.

**Do not confuse the three levels of evidence:** a technique can already exist; it can be proposed/experimented with; or it can be merged and verified. “Improvement considered” is not “implemented.” The list of 43 is an **evaluation inventory**, not 43 delivered changes.

### What we actually borrowed and shipped

| T3 Code-inspired lesson | Item(s) | Actual Gymtrack outcome |
| --- | --- | --- |
| Optimize the **workflow critical path** by splitting independent work | **#10, #11, #14, #15** | Four application lanes instead of one serial `pnpm check`; database now dominates runtime. |
| Strengthen input-bound dependency caches without sharing unsafe state | **#21, #22** | `pnpm-workspace.yaml` added to keys; one writer, 7d retention, branch identity preserved. |
| Benchmark the runner instead of assuming extra CPUs mean faster tests | **#19, #31, #32** | `medium` database experiment was slower/within noise, with twice the rate; reverted to `small`. |
| Protect exact-commit validation when introducing concurrency | **#3, #8, #12, #26** | All mandatory jobs remain unfiltered; global auto-cancel, database shards and test-result reuse deferred/rejected. |
| Measure **wall time separately from runner credits** | **#19, #22, #30–#32** | Historical 328.97s median; post-split seven-run provisional 216.8s median. Credit savings not claimed until the billed export. |

**Shipped speed mechanisms:** #11 and #14; #15's **inter-domain ordering** was corrected as part of the same split. **Strengthened existing safeguards:** #21–#22. **Evaluated then reverted:** #31–#32. The **seven-/six-job canonical evidence helper**, regression checks against filtered or missing jobs, and elimination of its caller-controlled override were necessary safety work **beyond the named 43 techniques**; the helper is tested, but GitLab's merge status does not independently run it as a live job-set audit.

### Full numbered checklist: original assessment vs final decision

**Baseline** reproduces the source audit's mutually exclusive classification (**7 missing, 12 improve, 15 not applicable, 4 already implemented, 4 needs evidence, 1 unsafe = 43**). **MR result** distinguishes shipped features, strengthened existing functionality, tests that were reverted, and work intentionally deferred.

#### 1–9. Smart execution and selection

| # | T3 Code-derived technique | Before MR !124 | Result in Gymtrack |
| ---: | --- | --- | --- |
| 1 | Changed-file detection | MISSING | Deferred — no safe change-path detector; all mandatory suites still run |
| 2 | Dependency-aware filtering | MISSING | Deferred — no validated reverse-dependency map |
| 3 | Conditional workflows/jobs | IMPROVE | Retained — feature/protected push routing; no new selective job filters |
| 4 | Affected-package tests | IMPROVE | Partial foundation — targeted web/API job commands exist; file-based routing deferred |
| 5 | Skip irrelevant file changes | MISSING | Deferred — docs-only optimization needs exact-job-set assurance |
| 6 | Conditional platform-specific tests | NOT APPLICABLE | N/A — no Windows/macOS application validation matrix |
| 7 | Independent early shard filtering | NOT APPLICABLE | N/A — no test shards to route yet |
| 8 | Superseded PR/MR pipeline cancellation | IMPROVE | Deferred — global auto-cancel could cancel important develop validation |
| 9 | Release concurrency/deduplication | NOT APPLICABLE | N/A — no active CircleCI release packaging/promotion |

#### 10–19. Parallel jobs and test execution

| # | T3 Code-derived technique | Before MR !124 | Result in Gymtrack |
| ---: | --- | --- | --- |
| 10 | Independent CircleCI jobs | ALREADY IMPLEMENTED | Extended — formerly separate DB/catalog; now four independent app lanes |
| 11 | Dedicated web suite | MISSING | SHIPPED — dedicated web-validation job with lint/type/test/build |
| 12 | Test sharding | MISSING | Deferred — isolated DB two-shard design requires full test union |
| 13 | Duration-weighted test distribution | MISSING | Deferred — duration balance only matters after safe shards exist |
| 14 | Parallel independent package tests | MISSING | SHIPPED — parallel web/API-unit/API-E2E/delivery jobs |
| 15 | Avoid unnecessary task dependency ordering | IMPROVE | SHIPPED (inter-domain) — replaced serial web→API→delivery, preserved in-job dependencies |
| 16 | Parallel typecheck/build | NEEDS EVIDENCE | Deferred — background TypeScript/build overlap untested on 1-vCPU executor |
| 17 | Concurrent safe setup steps | IMPROVE | Deferred — setup/apt duplication not yet worth a new image |
| 18 | Parallel packaging jobs | NOT APPLICABLE | N/A — no CircleCI release matrix |
| 19 | CPU/memory-aware executor concurrency | IMPROVE | Evaluated — measured CPU/memory constraints; kept safe default concurrency |

#### 20–30. Dependency and result caches

| # | T3 Code-derived technique | Before MR !124 | Result in Gymtrack |
| ---: | --- | --- | --- |
| 20 | Node/pnpm toolchain caching | IMPROVE | Retained — Node/pnpm pinned; no new custom toolchain image |
| 21 | Pinned version cache keys | ALREADY IMPLEMENTED | STRENGTHENED — cache key includes pnpm-workspace.yaml install policy |
| 22 | pnpm store caching | ALREADY IMPLEMENTED | STRENGTHENED — one writer, branch-scoped keys, 7-day retention |
| 23 | Filtered-install cache reuse | NEEDS EVIDENCE | Deferred — filtered installs risk missing root/delivery dependencies |
| 24 | Lockfile verification caching | NOT APPLICABLE | N/A — always keep --frozen-lockfile verification |
| 25 | Remote build/test task caching | NEEDS EVIDENCE | Deferred — no measured return from remote task-cache infrastructure |
| 26 | Safe per-test result caching | UNSAFE | Rejected unsafe — do not reuse stale test pass results for RLS/auth |
| 27 | Electron caching | NOT APPLICABLE | N/A — no Electron application workload |
| 28 | Playwright/browser caching | NOT APPLICABLE | N/A — Playwright not run in active CircleCI |
| 29 | Conditional native-dependency setup | NOT APPLICABLE | N/A — native build allowlist already controls this path |
| 30 | Cache-hit verification | IMPROVE | Partially analyzed — writer/reader cache timings; billed net saving still pending |

#### 31–35. Runners and checkout

| # | T3 Code-derived technique | Before MR !124 | Result in Gymtrack |
| ---: | --- | --- | --- |
| 31 | Fast runners; compare CircleCI resource classes, no Blacksmith | NEEDS EVIDENCE | Benchmark rejected — database medium did not outperform small |
| 32 | Right-size CPU/memory | IMPROVE | Tested and reverted — DB medium 220.6s vs small ~216–226s |
| 33 | Sparse checkout | NOT APPLICABLE | N/A — checkout ~1.3s, history required |
| 34 | Minimal change-detection checkout | NOT APPLICABLE | N/A — no dedicated changed-file detector; checkout cheap |
| 35 | Avoid unnecessary runtime/tool installation | IMPROVE | Deferred — no proved critical-path gain from changing apt/Node setup |

#### 36–39. Local validation and TypeScript

| # | T3 Code-derived technique | Before MR !124 | Result in Gymtrack |
| ---: | --- | --- | --- |
| 36 | Changed-file local validation | ALREADY IMPLEMENTED | Retained — documented targeted local check commands |
| 37 | Targeted tests | IMPROVE | Retained/organized — local focused test commands, CI full validation |
| 38 | Reuse safe successful local checks | NOT APPLICABLE | N/A for CI — local passes never certify the current SHA |
| 39 | Incremental TypeScript validation | IMPROVE | Retained capability — TypeScript incremental enabled; cross-run persistence deferred |

#### 40–43. Release and scheduling

| # | T3 Code-derived technique | Before MR !124 | Result in Gymtrack |
| ---: | --- | --- | --- |
| 40 | Reuse shared release build artifacts | NOT APPLICABLE | N/A in CircleCI — no shared release artifact consumer |
| 41 | Promote previously validated commits | NOT APPLICABLE | N/A in CircleCI — exact-SHA principle retained for future release gate |
| 42 | Filter deployment dependencies | NOT APPLICABLE | N/A in CircleCI — no active release dependency installer |
| 43 | Skip unnecessary scheduled pipelines | NOT APPLICABLE | N/A — no scheduled release validation pipeline |

### How to use this framework for the next decision

The highest-impact T3 Code lesson was **separate independent suites before tuning individual commands**. In Gymtrack, that yielded **~34.1% lower provisional median wall time**, comparing seven new `small` runs with the earlier 17-success median. The throughput result is not an exact-SHA A/B and compute credits rose because four jobs repeat checkout and install; the new monthly bill is still modeled.

**Most promising next *conditional* experiment:** #12–#13, two database shards, but only after a shard-aware `scripts/db/test-isolated.sh`, one standalone customer-database suite, two independently isolated PostgreSQL clusters, full file/test parity (RLS, tenant/site, finance, onboarding), proven full-job evidence, and an account-specific credit forecast below the **24k planning guardrail**. No shard or budget certification exists today.

**Rejected/avoided scope creep:** global auto-cancel (#8) because `develop` validation evidence must survive; per-test pass caches (#26) because they can hide security failures; `medium` (#31–#32) because its measured runtime was not better; path filtering (#1–#5) because a missing/filtered job might appear green without additional control-plane certification. Remote caches, special images and tooling are not justified for the now-database-bound critical path.

**Exact next evidence requests:** (1) fetch all six actual protected workflow jobs on merge SHA `a23dab5`; (2) accumulate **at least 10 comparable successful new runs** with configuration and cache cohort labels; (3) obtain real post-seven-day CircleCI compute/storage billed usage. The source audit's older classifications are a snapshot; this table is the delivery reconciliation, not a new performance benchmark.

**Sources:** [merged MR !124](https://gitlab.com/trackgym101/gymsas/-/merge_requests/124) · [full source audit](https://gitlab.com/trackgym101/gymsas/-/blob/a23dab522060be2fd0547952e84f85bbb34ab6e3/docs/ci/performance-audit.md) · [merged CircleCI config](https://gitlab.com/trackgym101/gymsas/-/blob/a23dab522060be2fd0547952e84f85bbb34ab6e3/.circleci/config.yml) · [canonical evidence helper](https://gitlab.com/trackgym101/gymsas/-/blob/a23dab522060be2fd0547952e84f85bbb34ab6e3/scripts/ci/circleci-evidence.mjs) · [CircleCI filtered `requires` rules](https://circleci.com/docs/reference/configuration-reference/#requires) · [CircleCI immutable caching/retention](https://circleci.com/docs/guides/optimize/caching/) · [CircleCI billed-credit model](https://circleci.com/docs/guides/plans-pricing/credits/).

---

## 13. Practical handoff

**Present state as of 2026-11-02:** MR !124 is merged into `develop`, exact-merge-SHA protected CircleCI is successful and CI runtime is materially lower in the observed post-split runs. Application functionality and hosted acceptance remain separate release gates.

**Next evidence tasks:** (1) retrieve the complete six-job protected workflow evidence for `develop@a23dab5` if a release-certification record needs it; (2) export billed CircleCI storage and compute usage after the seven-day retention window; (3) extend the seven provisional small-executor pilot observations to ten comparable runs, recording commit/config/cache cohorts; (4) continue Sprint 3's hosted security and deletion-policy work separately from this completed CI initiative. The private repository's audit/plan heading cleanup is optional documentation maintenance, not a reason to reopen the optimization.

**Sources of truth:**
- [Gymtrack MR !124, review and commits](https://gitlab.com/trackgym101/gymsas/-/merge_requests/124)
- [CircleCI implementation](https://gitlab.com/trackgym101/gymsas/-/blob/develop/.circleci/config.yml)
- [CI performance audit](https://gitlab.com/trackgym101/gymsas/-/blob/develop/docs/ci/performance-audit.md)
- [Optimization and rollback plan](https://gitlab.com/trackgym101/gymsas/-/blob/develop/docs/ci/optimization-plan.md)
- [Canonical evidence helper](https://gitlab.com/trackgym101/gymsas/-/blob/develop/scripts/ci/circleci-evidence.mjs)
- [CircleCI protected-branch post-merge workflow](https://app.circleci.com/workflow/792a3dc9-6b7b-4c34-96bc-5b768af5ee32)
- [CircleCI filtered dependencies](https://circleci.com/docs/reference/configuration-reference/#requires)
- [CircleCI cache semantics and retention](https://circleci.com/docs/guides/optimize/caching/)
