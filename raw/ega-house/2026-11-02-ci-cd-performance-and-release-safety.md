# EGA House — CI/CD optimization, hardening and measured delivery

**Date:** 2026-11-02  
**Status:** MERGED; full post-merge main CI verified SUCCESS  
**Repository:** [Ega-House-Platform](https://github.com/egawilldoit/Ega-House-Platform)  
**PR:** [#341 — ci: harden validation gates and parallelize web checks](https://github.com/egawilldoit/Ega-House-Platform/pull/341)  
**Source branch/head:** `docs/ci-performance-audit` at `00f623ed0ab764cdf0d0dfea941b4e3f9cd753b7`  
**Original main base:** `45f668a25e308a3d208acb83cde3605634e8fb25`  
**Merge commit:** [`4c426a1d0a6be1d2e22b34860ba96db7236b9001`](https://github.com/egawilldoit/Ega-House-Platform/commit/4c426a1d0a6be1d2e22b34860ba96db7236b9001), 2026-11-02 21:16:40 UTC  
**Scope:** GitHub Actions orchestration, CI gates, Android release evidence and CI verification only. No application source feature, SQL migration, lockfile or infrastructure change.

> **Evidence boundary:** This is an authored, dated delivery report grounded in PR code and GitHub Actions live run, job, and log data. The PR contains nine commits and changed twelve paths. Historical statistics, local regression suites and read-only release rehearsal details come from the source audit/implementation receipts where noted. The **live successful APK/OTA release evidence helper** was not invoked in this reporting task. CI success is not acceptance of authenticated product flows, Vercel Active CPU, deployment region or actual APK/OTA publishing.

## 1. Outcome

The main performance change removed a serial `web` bottleneck by running **web build/browser**, **web typecheck/font guard**, and **two Vitest test shards** as independent GitHub Actions jobs. Before enabling that parallelism, three batches hardened false-green validation, protected exact-commit release evidence and main-branch run concurrency. A final test change tied web-shard configuration to the mobile release verifier.

**Selected pre-split full-matrix baseline:** 346 seconds, with the web job taking 302 seconds. **Optimized full-matrix runs:** 141s, 172s, 250s and **190s post-merge on exact `main`**. The four-run descriptive median is **181 seconds (47.7% lower)**; the mean is **188.25 seconds (45.6% lower)**. The latest main run itself was **45.1% faster** than the selected baseline. These statistics compare a single baseline to a four-run, mixed-commit and mixed-event sample; they are NOT a statistically established long-run p50.

The improvement has a real resource trade-off: aggregate runner-minutes averaged **19.81** across optimized runs versus **17.85** for the selected baseline, approximately **11.0% higher**. That is **runner capacity**, not verified billing. The audit notes $0 billable standard hosted minutes for the public repository; this reporting pass did not obtain a separate billing export.

**What was not shipped:** no mobile release, no manual Vercel production deployment, no new paid CI provider, no new caches, no new database work, no third shard, and no declared Vercel Active CPU savings. Automatic Git-backed deployments, if triggered, are separate from this CI change and must be verified independently.

## 2. Baseline, methodology and corrected audit facts

The original Unified CI already had `dorny/paths-filter@v3`, independent package/domain/API/mobile/database jobs, an always-on `workspace` integrity and security job, and a required aggregate `hygiene` check. Fifteen historical required-check contexts had to remain stable.

The old web job ran typechecking, all web tests, production build, Playwright installation and workspace shell browser contracts **serially**. The audit sampled 100 workflow runs and a smaller same-configuration cohort. Independent review corrected major methodological errors:

| Topic | Corrected finding |
|---|---|
| Successful old web job duration | **p50 262s / p90 299s, n=49 successful**; 193s was a misleading all-execution median including interrupted jobs |
| Critical-path share | Web consumed roughly **89–94%** in inspected full workflows, not a blanket 95% |
| Same-config run count | **5**, inside the 100-run set, not three extra independent runs |
| Vitest version | Installed **4.1.5**, though range declared `^4.0.7` |
| Shard algorithm | Vitest hashes paths then distributes **by file count**, not duration-weighted scheduling |
| npm install-links | Workspace packages remain symlinks; `install-links` primarily affects `file:` dependencies |
| setup-node npm cache | Key includes OS/architecture/npm/lockfile content, **not Node major**; workflow separately pins Node 22 |
| Runner concurrency | The plan assumed an account-level limit, not per-repo; actual plan usage was not independently certified |
| Root tsconfig claim | Agent Runner uses independent tsconfig, so the asserted root-config coverage gap was rejected |
| Build typechecking | The original Next.js build log included a separate **25.1s TypeScript phase**; dedicated typecheck still provides explicit evidence |

In the specific pre-split full matrix [run 41485736069](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41485736069), the web job was **302s** of a **346s** workflow. Baseline web suite covered **306 files / 2,313 Vitest tests**. This run is the meaningful selected structural comparator; docs-only partial runs should never be mixed into it.

## 3. Changes, why they were required, and actual behavior

### Batch 0A — Fail-closed aggregate validation (`f2a39eb7`)

The original gate could treat an **unexpected skipped job as acceptable** when force-full was not active. PR #341 installed `scripts/ci/aggregate-gate.mjs` and made the required `hygiene` job evaluate the actual GitHub Actions `toJSON(needs)`.

The gate requires `changes` and `workspace` success, validates **eight routing outputs** as exact text `true`/`false`, requires all expected leaf jobs to succeed, and rejects missing/unknown dependency keys, failure, cancellation, invalid routing, or a skipped job whose scope should have run. A genuinely inactive path-filtered job may skip. `lint-report` remains expressly informational. The existing required job names were preserved; no branch-protection edit was necessary.

Crucially, the always-running `workspace` now executes `npm run test:ci-workflow`, so gate and workflow regression tests execute in real CI. Post-merge main job logs confirmed **87 tests passed, zero failed** in that suite.

### Batch 0B — Strict mobile APK/OTA release evidence (`ee802276`)

Both `.github/workflows/mobile-delivery.yml` and `mobile-ota.yml` now invoke `scripts/ci/release-evidence.mjs`. Previously, **any successful Unified run for the SHA** could be accepted, even if most conditional jobs had skipped.

The validator selects an exact 40-character source SHA from the right repository and Unified workflow path; it accepts only completed, successful `push` or `workflow_dispatch` runs on `main`. It then fetches jobs from **one run and one run attempt**, not mixed retries. The contract demands every mandatory job succeed exactly once, while advisory `lint-report` does not block.

There are **17 mandatory API job names**: `changes`, `workspace`, `contracts`, `domain`, `application`, `data-access`, `regressions`, `lint-changed`, `web`, `web-typecheck`, `mobile`, `server`, `api-client`, `db-invariants`, `hygiene` and the separate `web-tests (1)`, `web-tests (2)`. Missing/failed/skipped/cancelled/duplicate required jobs fail; API failures fail closed. This is stricter than GitHub’s overall green workflow conclusion.

**Three real read-only negative rehearsals** were recorded:
- Partial green main push [41349819428](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41349819428): **rejected with 12 errors** for missing/skipped jobs.
- Full green **PR** run [41485736069](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41485736069): **rejected**, `pull_request` is not trusted production evidence.
- Unknown SHA: **rejected**, no such validation run.

Fixtures verify the positive logic, but the live CLI **positive** path was not executed. No APK/OTA build was dispatched as part of this work.

### Batch 0C — Main concurrency and dependency-policy routing (`c095a5ca`)

Ordinary PR runs retain a stable PR group and cancel superseded validation. **Push, manual dispatch, and full-matrix release PR** runs use unique `github.run_id` groups. This avoids both running-run cancellation and GitHub’s default **pending run replacement**. Group names are isolated from other workflows. The implementation is unit/config-tested; a deliberate live double-main-push rehearsal has **not** occurred.

`.npmrc` was added to **all eight filters** (web, server, mobile, API client, database, packages, code, regressions), so changes to install policy trigger the relevant checks. Filter-replay tests cover reverse dependencies such as web→server code, server→data access, mobile→API client and database→Drizzle. Over-triggering is deliberate; under-triggering could produce false green.

### Batch 1 — Split web validation and shard Vitest (`f28f0516`)

The original web required check name remains. Three job types now execute concurrently:

| Job | What it runs | Coverage |
|---|---|---|
| `web` | Production build, Chromium + system dependencies and workspace shell browser contracts | Build/Playwright remains required |
| `web-typecheck` | Explicit web TypeScript and font egress checks | No loss of type protection |
| `web-tests (1)` | Vitest `--shard=1/2` | **153 files; 1,099 tests** |
| `web-tests (2)` | Vitest `--shard=2/2` | **153 files; 1,214 tests** |

Both shards together provide **306 files / 2,313 tests**, exactly the unsharded suite, with no omitted or overlapping files according to the PR verification. `fail-fast: false` means both shards report outcomes even when a peer fails; each shard retains serial-internal file execution/isolation. The `hygiene` gate includes `web`, `web-typecheck` and the matrix job. The production release verifier requires both API shard names individually.

The new work ran **in parallel** with unchanged package, API, mobile, DB, security and architecture checks. No tests were deleted.

### Final shard/release drift fix (`540586cb`)

`validateShardContract()` reads the real YAML workflow matrix in the CI guardrail tests and verifies it agrees with the release helper’s fixed two-job contract. `[1,2]` and reordered `[2,1]` pass; `[1]`, duplicate values and `[1,2,3]` fail. Runtime release evidence additionally rejects any unexpected `web-tests (3)` job. This closes a future accidental extra-shard gap without changing the actual two-shard job graph.

### Documentation and Batch 2 evaluations (`de5f8c22`, `00f623ed`)

The PR audit/plan recorded the benchmark, reviewed follow-ups and classified caching/runner alternatives. They remain historical documents at merge time; any text still saying “not merged” is a dated drafting status, superseded by the GitHub merge commit and this Atlas handoff. No further speed code was added after the successful split beyond the shard safety fix.

## 4. All implementation commits and changed files

| Commit | Purpose |
|---|---|
| [`447ae5a`](https://github.com/egawilldoit/Ega-House-Platform/commit/447ae5a534d9d710e2e3aec9f79481be97fd3dc1) | Initial performance audit and optimization plan |
| [`d664ad8`](https://github.com/egawilldoit/Ega-House-Platform/commit/d664ad8faf04472029da419f3cb26a0990e47367) | Independent review corrections |
| [`f2a39eb`](https://github.com/egawilldoit/Ega-House-Platform/commit/f2a39eb7c816bac028b90ae8fdcf0e50fef2a15d) | Aggregate gate and CI-workflow tests |
| [`ee802276`](https://github.com/egawilldoit/Ega-House-Platform/commit/ee802276e832b7dc3960e0971977d59dd937e0a4) | Exact-SHA/run/attempt mobile release evidence |
| [`c095a5ca`](https://github.com/egawilldoit/Ega-House-Platform/commit/c095a5cae92c05e6c30954ea461386353c7408eb) | Concurrency and npm configuration routing |
| [`f28f0516`](https://github.com/egawilldoit/Ega-House-Platform/commit/f28f051633a8a3815b34649148ab5db3621ed49a) | Parallel build/typecheck/two test shards |
| [`de5f8c22`](https://github.com/egawilldoit/Ega-House-Platform/commit/de5f8c22e6d8ef072781400e4f9e59fda22b20e2) | Results and Batch 2 decision documentation |
| [`540586cb`](https://github.com/egawilldoit/Ega-House-Platform/commit/540586cbb35be979197a1ac6d77d4f9d2e9ddc9a) | Reject shard drift |
| [`00f623ed`](https://github.com/egawilldoit/Ega-House-Platform/commit/00f623ed0ab764cdf0d0dfea941b4e3f9cd753b7) | Final report and release rehearsal |
| [`4c426a1d`](https://github.com/egawilldoit/Ega-House-Platform/commit/4c426a1d0a6be1d2e22b34860ba96db7236b9001) | Merge into `main`, 2026-11-02 21:16:40 UTC |

**Twelve changed files:**

```text
.github/workflows/mobile-delivery.yml
.github/workflows/mobile-ota.yml
.github/workflows/unified-platform-validation.yml
docs/ci/optimization-plan.md
docs/ci/performance-audit.md
package.json
scripts/ci/aggregate-gate.mjs
scripts/ci/aggregate-gate.test.mjs
scripts/ci/mobile-delivery-guardrails.test.mjs
scripts/ci/release-evidence.mjs
scripts/ci/release-evidence.test.mjs
scripts/ci/unified-ci-force-full.test.mjs
```

No application source feature, DB schema, lockfile, paid runner, or branch-protection change was included. Existing 15 required check names were preserved.

## 5. Five measured full-matrix workflows and true gains

All run-level wall times were independently checked against GitHub Actions run metadata; job conclusions and post-merge durations against the jobs API. `Runner-min` sums actual job durations (concurrent jobs count separately).

| Stage | Run | SHA | Full-matrix wall | Aggregate runner-min | Long job |
|---|---|---|---:|---:|---|
| Before split | [41485736069](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41485736069) | `c095a5ca` | **346s** | **17.85** | web 302s |
| After A | [41488220024](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41488220024) | `f28f0516` | **141s** | **16.68** | shard 1, 106s |
| After B | [41489062225](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41489062225) | `de5f8c22` | **172s** | **18.22** | web 135s |
| Last PR | [41498201639](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41498201639) | `00f623ed` | **250s** | **23.02** | shard 1, 143s |
| **After merge** | [41526874883](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41526874883) | `4c426a1d` | **190s** | **21.32** | db-invariants 161s |

```text
Optimized wall = [141, 172, 250, 190] seconds
Sorted = [141, 172, 190, 250]
Median = (172 + 190)/2 = 181 s
Mean = (141 + 172 + 250 + 190)/4 = 188.25 s
Median improvement vs selected 346s baseline = 47.69%
Mean improvement vs selected baseline = 45.59%
Post-merge run improvement vs baseline = 45.09%
Optimized aggregate runner-min mean = 19.81 vs baseline 17.85 (+10.98%)
```

The **250s** run is not a pure hygiene anomaly: `changes` 43s and `hygiene` 53s were unusually slow, but shard 1 took **143s**, web **137s**, workspace **93s**. The **190s** main run instead had a DB bottleneck (`db-invariants` **161s**, plus mobile 136s and web 126s). This explains performance variance. The pre-change web bottleneck dropped from **302s** to web lanes of **106–143s** observed in PR runs, while post-merge `web` took 126s and shard 1 122s.

**Confidence:** structural parallelism and test parity are established. The exact percentage is an observed small-cohort comparison (**n=4 optimized, n=1 selected baseline**), not a stable comparative p50/p90. Collect at least ten comparable full-matrix workflows, labeling PR versus push, filters, cache state, and runner/queue variations.

**Resource trade-off:** more parallel jobs need more checkout/setup/npm ci. Faster wall clock does not imply fewer aggregate runner-minutes. No billed cost or Vercel CPU reduction has been inferred from these data.

## 6. Post-merge green CI: independent exact-SHA proof

GitHub confirms PR #341 was **merged** to `main` at **4c426a1d0a6be1d2e22b34860ba96db7236b9001**.

Automatic `push` [Unified Platform Validation run 41526874883](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41526874883) was created **21:16:44 UTC**, finished **21:19:54 UTC**, with **conclusion success**, attempt 1, exact merge SHA. All **18 job records** were retrieved: **17 successful**, only advisory `lint-report` skipped. No required job was failed, cancelled or omitted.

The post-merge `hygiene` log explicitly listed `changes`, `workspace` and every required routed job `result=success expected=yes`; `lint-report result=skipped expected=no`. The full required job set therefore **actually ran**, not merely a docs-only path-filtered green workflow.

The `workspace` log recorded `npm run test:ci-workflow` **87/87 passing** and `test:architecture` **21/21 passing**. Both Vitest shard logs reported **153 passed files each**. Other jobs for builds, browser contracts, server/API, mobile, DB and domain/packages also succeeded. A separate local completion report recorded **50/50 guardrail tests**.

This is the first **live completed full validation** on merged main for the new matrix. It is not itself a mobile release, authenticated browser acceptance, Vercel production deployment verification, or a positive execution of the release-helper CLI.

## 7. Release acceptance and exact safety boundary

**Main push success ≠ APK/OTA automatically released.** The new helper requires trusted main SHA, complete single run+attempt, plus the 17 job names. The successful merge push **appears to provide a candidate full matrix**, but the release helper's live positive command was **not** executed here. Do not assert that positive proof exists without running it.

Before any APK/OTA, run:
```bash
node scripts/ci/release-evidence.mjs \
  --sha 4c426a1d0a6be1d2e22b34860ba96db7236b9001 \
  --repo egawilldoit/Ega-House-Platform
```

It should be run in an authorized environment with GitHub Actions read access. If the existing main push run is accepted, **do not manufacture a redundant dispatch**. If rejected (for example, because a newer partial run is chosen), dispatch `Unified Platform Validation` with `force_full=true` for the exact main SHA and confirm all required jobs before release. The owner must separately authorize any APK or OTA publication.

**Known fail-closed availability limitation:** the helper selects the newest trusted green run before verifying that its jobs are complete. A newer partial green run could cause a rejection despite an older complete green run for the same SHA. That cannot yield a false-positive release; it may require a new full run. No change to this policy was made here.

## 8. Accepted technical evidence and QA guarantees

| Contract | Proof |
|---|---|
| Expectation-aware aggregate | Unit tests exercise failure, cancellation, skipped expected jobs, missing keys, invalid routing and unknown needs |
| All required existing statuses | 15/15 original names intact; main full run successful |
| Force-full | `workflow_dispatch force_full=true` and special release/full-matrix branches execute every leaf |
| Test parity | **306/2313**, shard file union 153+153, test totals 1099+1214 from implementation evidence |
| CI workflow regressions | 87/87 passing in merged `workspace` log |
| Security, dependencies, architecture | Always-on `workspace` success; 21/21 architecture tests in its log |
| Browser build and contracts | `web` production build/Playwright steps green |
| Web TypeScript/font | `web-typecheck` green |
| DB, server, API, mobile | Individual jobs green on exact merged SHA |
| Mobile partial/pr/unknown release evidence | Three live **negative** rehearsals rejected correctly |
| Shard change drift | Real YAML contract test and runtime unexpected-shard rejection |
| Main double-push cancellation behavior | Static/config verified only; not deliberately exercised live |
| Live successful mobile-release evidence | **Not executed**; required before publish |

These are CI/unit/browser-contract proofs only. They do not certify runtime sign-in, hosted migration state, production region, actual Active CPU reduction, or an APK installation.

## 9. Batch 2 choices: reject scope creep

| Idea | Observation | Decision |
|---|---|---|
| Playwright browser cache | 17s setup after split, ~9s download share; web no longer consistently critical | **Reject**; no demonstrated critical path gain |
| Cache `node_modules` | `npm ci` ~33–38s per shard; modeled ~24s improvement | **Defer**; provenance, native bindings, clean-install fallback absent |
| Upload/reuse `.next` | Build/browser already co-located; transfer costs time and adds edges | **Reject** |
| Extra shard / duration balancing | Two shards preserve full suite; test time spread (62 vs 55s in initial CI) modest | **No change**; sample more first |
| Blacksmith migration | Standard GitHub-hosted runner chosen; paid/capped third-party runner adds risk | **Reject** for Unified, preserve existing Gradle-only usage |
| Narrow routing filters | Conservative consumer mapping protects hidden dependencies | **Defer** unless safe exact coverage can be proved |
| Global/test result caching | Cache hits cannot replace current-SHA correctness checks | **Do not introduce** |

**No new Node cache, Playwright cache, runner provider, hosted paid service or third shard** was shipped. Those were examined, not implemented.

## 10. Boundaries relative to EGA House product work

This delivery is separate from persistent workspace-shell [PR #339](https://github.com/egawilldoit/Ega-House-Platform/pull/339), the [PR #337](https://github.com/egawilldoit/Ega-House-Platform/pull/337) Active CPU/realtime initiative, Vercel regional placement, and browser-authentication E2E. The earlier shell-era CI comparison (331→285s on different SHAs) is a *different optimization period* and **must not be added to the PR #341 346s baseline delta**.

No application features, database migrations, lockfile dependencies, Production regions or Vercel deployment permissions changed in PR #341. A green Actions run on 4c426a1 is not evidence that production Vercel web/API already served that SHA; that requires its own provider evidence.

## 11. All 43 audit techniques — implemented vs evaluated

The source [43-item audit](https://github.com/egawilldoit/Ega-House-Platform/blob/4c426a1d/docs/ci/performance-audit.md) had **19 already implemented, 6 improve, 10 missing and 8 not applicable** before PR #341. This matrix separates actual merged changes from techniques merely assessed; it is **not 43 shipped improvements**.

### Execution and filtering (#1–#9)

| # | Technique | Pre-PR status | PR #341 outcome | Why |
|---:|---|---|---|---|
| 1 | Change detection | ALREADY IMPLEMENTED | **Retained** | dorny/paths-filter remains |
| 2 | Dependency-aware filtering | ALREADY IMPLEMENTED | **Extended and replay-tested** | Consumer closure preserved for web/server/mobile/database |
| 3 | Conditional jobs | ALREADY IMPLEMENTED | **Hardened** | force_full and expected-job routing checked |
| 4 | Exact affected-package selection | IMPROVE | **Deferred** | Conservative filters preferred |
| 5 | Ignore irrelevant changes | ALREADY IMPLEMENTED | **Retained plus .npmrc fix** | Docs-only may skip; dependency-policy changes now route |
| 6 | Platform-specific tests | NOT APPLICABLE | **N/A** | Linux-only Unified CI |
| 7 | Early shard filtering | ALREADY IMPLEMENTED | **Extended** | New web-test matrix depends on changes |
| 8 | Cancel superseded runs | IMPROVE | **SHIPPED** | Stable PR group and unique main/dispatch/full-matrix run IDs |
| 9 | Release concurrency | ALREADY IMPLEMENTED | **Retained** | Existing mobile-workflow release groups untouched |

### Parallelism (#10–#19)

| # | Technique | Pre-PR status | PR #341 outcome | Why |
|---:|---|---|---|---|
| 10 | Independent concurrent jobs | ALREADY IMPLEMENTED | **Extended** | New web lanes run alongside existing jobs |
| 11 | Dedicated web test jobs | MISSING | **SHIPPED** | Web build/browser separated from Vitest and TypeScript |
| 12 | Test-suite sharding | MISSING | **SHIPPED** | Two Vitest shards preserve 306 files / 2313 tests |
| 13 | Duration-weighted sharding | MISSING | **Deferred** | Vitest uses file-hash/count partition, not LPT timing |
| 14 | Parallel package tests | ALREADY IMPLEMENTED | **Retained** | Existing package jobs unchanged |
| 15 | Remove unnecessary job dependencies | ALREADY IMPLEMENTED | **Retained** | Leaf jobs independent; hygiene aggregates |
| 16 | Parallel typecheck/build | MISSING | **SHIPPED** | web-typecheck independent of web build and web-tests |
| 17 | Overlap independent setup | ALREADY IMPLEMENTED | **Retained** | Separate per-job setup, no custom runner |
| 18 | Parallel release packaging | NOT APPLICABLE | **N/A** | Single APK release deliberately serialized |
| 19 | Resource-aware concurrency | IMPROVE | **Measured, no tuning** | Account capacity and queue still follow-ups |

### Caching (#20–#30)

| # | Technique | Pre-PR status | PR #341 outcome | Why |
|---:|---|---|---|---|
| 20 | Toolchain caching | ALREADY IMPLEMENTED | **Retained** | setup-node npm cache |
| 21 | Version-specific keys | ALREADY IMPLEMENTED | **Retained caveat** | Node major not part of setup-node cache key |
| 22 | npm store caching | ALREADY IMPLEMENTED | **Retained** | Existing dependency-store cache |
| 23 | Filtered-install cache reuse | ALREADY IMPLEMENTED | **Retained** | One shared npm cache store |
| 24 | Cache lockfile verification | NOT APPLICABLE | **Not applicable** | Integrity executed every run |
| 25 | Remote task-result cache | NOT APPLICABLE | **Not adopted** | No new Nx/Turbo/Bazel or paid service |
| 26 | Per-test result cache | NOT APPLICABLE | **Not adopted** | Cannot allow stale security tests |
| 27 | Electron cache | NOT APPLICABLE | **N/A** | No Electron workload |
| 28 | Playwright binary cache | MISSING | **REJECTED** | About 9s download cacheable and web not critical |
| 29 | Conditional native/system libraries | IMPROVE | **Deferred unsafe** | Preserve Chromium --with-deps until proven |
| 30 | Cache outcome verification | ALREADY IMPLEMENTED | **Retained** | Existing cache hit/miss observability |

### Runners and checkout (#31–#35)

| # | Technique | Pre-PR status | PR #341 outcome | Why |
|---:|---|---|---|---|
| 31 | Blacksmith CI runners | ALREADY IMPLEMENTED | **No expansion** | Existing Gradle-only usage retained |
| 32 | Runner sizing | ALREADY IMPLEMENTED | **Retained** | ubuntu-latest in Unified CI |
| 33 | Sparse checkout | MISSING | **Rejected low value** | 1–5s checkout, full-tree checks need source |
| 34 | Shallow detector checkout | IMPROVE | **Deferred low value** | Existing change detector cheap; push requires history |
| 35 | Avoid unused toolchains | ALREADY IMPLEMENTED | **Retained** | Node in PR CI, Android toolchain separate |

### Local developer loop (#36–#39)

| # | Technique | Pre-PR status | PR #341 outcome | Why |
|---:|---|---|---|---|
| 36 | Changed-path local checker | MISSING | **Deferred** | lint:changed exists, no comprehensive check:changed |
| 37 | Targeted local tests | MISSING | **Deferred** | No new vitest related/changed command |
| 38 | Reuse local success as CI | NOT APPLICABLE | **Not adopted** | CI must prove exact SHA |
| 39 | Incremental TypeScript state cache | MISSING | **Deferred** | tsconfig incremental present but cross-run tsbuildinfo not reused |

### Release (#40–#43)

| # | Technique | Pre-PR status | PR #341 outcome | Why |
|---:|---|---|---|---|
| 40 | Build artifact reuse | MISSING | **REJECTED** | Keep build + browser colocated, avoid .next transfer |
| 41 | Promote validated commits | IMPROVE | **SHIPPED safety fix** | Both APK/OTA require exact SHA complete trusted run/attempt |
| 42 | Release dependency filtering | ALREADY IMPLEMENTED | **Retained** | Mobile tags/dispatch only |
| 43 | Scheduled release filtering | NOT APPLICABLE | **N/A** | No scheduled release workflow |

**Shipped speed techniques:** #11, #12 and #16. **Shipped main-run safety:** #8, #41, plus strict hygiene, route tests, release-attempt and shard-contract enforcement (the last items are cross-cutting safeguards). Existing execution/caching behavior was retained unless named otherwise. In particular: no Playwright, node_modules, remote or build-artifact caches; no third shard; no larger or paid runner.

## 12. Original investigation: how we found the problem

The work started in a single `docs/ci-performance-audit` worktree created from `origin/main@45f668a2`, after PR #340. The independent audit and plan preceded implementation. The agent explicitly left an unrelated local `supabase/.temp/cli-latest` modification untouched. This describes the original agent's recorded work; no 100-run audit was repeated by this Atlas edit.

The investigation used the **GitHub CLI (`gh`)**, GitHub Actions **runs/jobs/timings REST endpoints**, job logs, current workflow YAML, PR changed-file lists and a local Vitest per-file timing report. The historical [source audit](https://github.com/egawilldoit/Ega-House-Platform/blob/4c426a1d/docs/ci/performance-audit.md) contains the original selection criteria, dated run IDs and findings.

| Investigation | Original sample | Key evidence and conclusion |
|---|---|---|
| Workflow inventory | 100 Unified runs, 2026-10-29 01:37–2026-11-01 21:06 UTC | 34 successes; mixed-scope success p50 **281s**, p90 **331s** |
| Current configuration | Five runs after `dee17084`, already among the 100 | Do not double-count a separate five-run sample |
| Old web bottleneck | 49 successful `web` jobs | Successful-only p50 **262s**, p90 **299s**; the 193s all-execution median included aborts |
| Changed-file routing | PRs #328–#340, twelve PRs | Seven nearly full-matrix due package/server fan-out; existing detector inexpensive, conservative |
| Job/step logs | Runs [41349819428](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41349819428), [41348572389](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41348572389), [41335541779](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41335541779) | `npm ci` 24–40s even with cache hit; all-web Vitest 120–125s; build 35–55s |
| Vitest local JSON report | 306 test files | Path import/environment/isolation dominated the ~120s suite; only ~22s assertions |
| Queue and concurrency | 100-run history | Queue p50 **2s**, p90 **84s**, one unexplained 92-minute outlier; one cancelled `main` push (run `40821834402`) |
| Billing | Historical public-repo run | Actions timing API `total_ms=0` billable; job capacity still consumed |

**The old job's actual dependency:** typecheck → all web tests → production build → install Playwright → browser contracts. In one 299s successful web job, the audit reports about **39s npm ci**, **24s typecheck**, **125s tests**, **55s build**, **36s Chromium setup**, and **6s browser contracts**, plus setup/checkout and per-job overhead. A 320s web-only workflow was reconciled as **11s initial delay + 299s web + 2s handoff + 7s hygiene + 1s completion tail**. The rounded individual step times are not exact-additive.

**What the audit ruled out:** replacing the already-correct `dorny/paths-filter@v3`, paid runner migration, risky test-result caching, enabling Vitest `fileParallelism` against the isolation policy, and adding a new build framework for a short pipeline. It identified a missing `.npmrc` filter (fixed in #341) and a separate `vercel.json` routing/deployment-validation gap (**not fixed** by #341).

## 13. Precise implementation method and safety dependencies

```text
BEFORE:
changes -> web [typecheck -> 306-file Vitest -> build -> Playwright -> browser] -> hygiene
        -> other package/API/mobile/db jobs (already independent) ----------^

AFTER:
changes -> web [build -> Playwright -> browser] ----------------------------+
        -> web-typecheck [TypeScript + font guard] -------------------------+
        -> web-tests (1) [--shard=1/2] -------------------------------------+-> hygiene
        -> web-tests (2) [--shard=2/2] -------------------------------------+
        -> existing package/API/mobile/db jobs ----------------------------+
```

**Batch 0A — make the aggregate fail closed:** `scripts/ci/aggregate-gate.mjs` receives `toJSON(needs)` inside `hygiene`. Its contract knows **16 dependency job IDs**, validates eight exact `true`/`false` routing outputs, requires `changes` and `workspace` even for docs-only changes, and treats unexpected skip/failure/cancellation/missing/unknown job as an error. Informational `lint-report` is allowed to skip. **35 gate unit cases** protect negative and positive states. Workflow tests were added to the always-on `workspace` job.

**Batch 0B — protect APK and OTA:** Both mobile workflow files now call the same `scripts/ci/release-evidence.mjs` rather than an inline `gh api` check for any green run. They add `actions: read` permissions and use the workflow-provided GitHub token. The helper requires a trusted complete `main` `push`/`workflow_dispatch` run for the exact source SHA, correct repo/workflow, **one run attempt**, and **all 17 named API jobs**, including both shards. Missing/extra/duplicate/skipped/failed/cancelled jobs fail; green PR evidence does not qualify. **37 release-evidence unit cases** and read-only negative rehearsals back this up. No new token or paid service was needed.

**Batch 0C — fix concurrency and paths:** Ordinary PR updates cancel superseded work via a stable PR group. Main pushes, manual dispatches and `release/full-matrix/*` PRs use a unique `github.run_id` group. This avoids the GitHub [documented replacement of older pending runs](https://docs.github.com/en/actions/how-tos/write-workflows/choose-when-workflows-run/control-workflow-concurrency), even with `cancel-in-progress:false`. `.npmrc` was added to **all eight** path filters; regression tests replay web→server, server→data-access, mobile→api-client and database→Drizzle dependencies. A deliberately timed live double-main-push was **not** performed.

**Batch 1 — remove serial web work:** Keep the original required `web` check name and production build/browser contracts; run typecheck/font checks in `web-typecheck`; run two independent `web-tests` jobs with Vitest's `--shard=1/2` and `2/2`, `fail-fast:false`. Preserve serial file isolation within each shard, rather than using unverified in-process file parallelism. The new lanes all participate in `hygiene.needs`. No app tests were deleted.

**Batch 1b — stop silent shard drift:** The real YAML matrix is compared to the release helper's shard contract; `[1,2]` and `[2,1]` pass, while `[1]`, `[2,3]`, `[1,2,3]` and duplicates fail. The release helper also rejects a runtime `web-tests (3)` job, instead of silently ignoring it. This was the last code correction before the documentation-only final commit.

## 14. Complete job-duration evidence

**Job runtime seconds** from GitHub Actions `completed_at - started_at`, not workflow wall clock. A dash means the lane did not exist before the split.

| Job | Baseline 346s | Optimized A 141s | Final PR 250s | Post-merge 190s |
|---|---:|---:|---:|---:|
| changes | 8 | 7 | 43 | 6 |
| workspace | 75 | 68 | 93 | 68 |
| contracts | 43 | 37 | 41 | 55 |
| domain | 51 | 57 | 58 | 39 |
| application | 62 | 67 | 59 | 64 |
| data-access | 50 | 55 | 62 | 69 |
| regressions | 78 | 52 | 58 | 53 |
| lint-changed | 55 | 45 | 68 | 68 |
| web | **302** | 92 | 137 | 126 |
| web-typecheck | — | 74 | 93 | 74 |
| web-tests (1) | — | 106 | 143 | 122 |
| web-tests (2) | — | 101 | 104 | 88 |
| mobile | 123 | 81 | 124 | 136 |
| server | 71 | 49 | 68 | 73 |
| api-client | 52 | 35 | 78 | 62 |
| db-invariants | 93 | 65 | 99 | **161** |
| lint-report | skipped | skipped | skipped | skipped |
| hygiene | 8 | 10 | 53 | 15 |

**Bottleneck shifted:** the old 302s `web` job became a 106s first-run web shard; by the final PR run the longest web shard was 143s, and the merged run's slowest job was `db-invariants` at 161s. The 250s PR result was not **only** slower `changes` (43s) and `hygiene` (53s): web/shard and other jobs also ran slower.

### Directly read post-merge job logs

[Unified run 41526874883](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41526874883) is `event=push` on the exact `main@4c426a1d0a6be1d2e22b34860ba96db7236b9001`, attempt **1**, **success**, **18 job records: 17 successful and informational `lint-report` skipped**.

| Proof | Exact live result |
|---|---|
| [Web shard 1 log](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41526874883/job/125556832847) | **153 files passed; 1,099 tests passed** |
| [Web shard 2 log](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41526874883/job/125556832807) | **153 files passed; 1,214 tests passed** |
| [Web build/browser log](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41526874883/job/125556832870) | Production build and **2 Playwright shell tests** passed |
| [Workspace log](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41526874883/job/125556761926) | **87/87** CI workflow cases and **21/21** architecture cases |
| [Hygiene log](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41526874883/job/125558267837) | Each mandatory dependency `result=success expected=yes`; `lint-report skipped expected=no` |
| [Web typecheck job](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41526874883/job/125556832662) | Typecheck/font-guard job successful |

This establishes **306 passed files / 2,313 passed tests on the actual merged commit**, not merely on the earlier PR. The post-merge run was **not manually forced full**: workflow changes in `.github/workflows/**` triggered all conservative consumer routes, resulting in a real complete matrix. The helper's release contract checks **actual job results and source identity**, not whether the run was manually dispatched.

## 15. Tools, actual commands, deferred work and verification boundary

**Technical tools used or preserved:** GitHub CLI, Actions REST job/attempt/log APIs, `dorny/paths-filter@v3`, `actions/checkout@v5`, `actions/setup-node@v6`, Node **22**, existing npm store caching, reproducible `npm ci`, installed Vitest **4.1.5**, Playwright Chromium, Next.js build, Node built-in `node:test`, `toJSON(needs)`, GitHub `github.run_id` concurrency, and `ubuntu-latest` GitHub-hosted runners. The prior Blacksmith Gradle-only mobile build policy was **not expanded**. The source code does not independently identify which proprietary T3 Code agent models/subagents authored the commits, so the report does not invent them.

**Read-only measurements (GitHub CLI examples):**

```bash
gh run list --repo egawilldoit/Ega-House-Platform --workflow unified-platform-validation.yml --limit 100
gh run view 41526874883 --repo egawilldoit/Ega-House-Platform --log
gh api 'repos/egawilldoit/Ega-House-Platform/actions/runs/41526874883/jobs?per_page=100'
gh api 'repos/egawilldoit/Ega-House-Platform/actions/runs/41526874883/attempts/1/jobs?per_page=100'
```

**Implementation/CI test commands** (documented, **not all rerun during this Atlas writing pass**):

```bash
npm ci --no-audit --no-fund
npm run test:ci-workflow
npm run test:guardrails
npm run test:architecture
npm run ci:workspace
npm run check:architecture
npm run typecheck
npm run build
npm run test --workspace @ega/web -- --shard=1/2
npm run test --workspace @ega/web -- --shard=2/2
```

The `test:ci-workflow` package script invokes the real workflow-shape, aggregate-gate and release-evidence test files. **87** such tests passed live on merged `main`. A separate agent completion report recorded **50 guardrail tests**; that count was not independently re-executed in Atlas.

**Read-only APK/OTA release-evidence command (next operational proof, not executed here):**

```bash
node scripts/ci/release-evidence.mjs \
  --sha 4c426a1d0a6be1d2e22b34860ba96db7236b9001 \
  --repo egawilldoit/Ega-House-Platform
```

A positive invocation of the actual helper remains outstanding. The main push's recorded jobs **appear to satisfy** its contract, but a newer partial run of the same SHA could be chosen and cause a **fail-closed false negative**. Only if necessary, an authorized full-matrix dispatch may be used: `gh workflow run unified-platform-validation.yml --ref main -f force_full=true`. None of these steps publishes an APK or OTA; publishing requires its own authorization.

**Why measured speed differs from runner resource use:** wall clock is `run.updated_at - run.created_at`; runner minutes sum every individual job interval, including overlaps. The selected **346s** pre-split full-matrix run is not the historical mixed-scope p50 of 281s. Four optimized runs **141/172/250/190s** have provisional median **181s** and mean **188.25s**, but runner consumption averaged **19.81min vs 17.85min** at baseline. This is a small n=4/n=1 comparison, **not** ten-run p50/p90 certification, actual account billing or evidence of reduced Vercel Active CPU.

**Rejected rather than shipped:** Playwright download cache, `node_modules` cache without provenance/clean fallback, third shard, cross-job `.next` artifact transfer, remote task cache, runner-provider migration, more aggressive selective filtering, test-result reuse, and in-process parallel test files. These must remain recorded as experiments or rejected proposals, not as the 43 “delivered improvements.”

**Rollback boundaries:** reverting `f28f0516` restores serial web checks but requires consistent `hygiene.needs` and release shard-contract updates. Reverting `540586cb` loses drift protection; reverting `c095a5ca` reintroduces main run replacement and `.npmrc` routing risk; reverting `ee802276` or `f2a39eb` reintroduces release or aggregate false-green defects. Rollback was **not** performed and must be a separately tested change.

**Evidence links:** [Original audit and 43-point matrix](https://github.com/egawilldoit/Ega-House-Platform/blob/4c426a1d/docs/ci/performance-audit.md) · [Optimization plan and batch metrics](https://github.com/egawilldoit/Ega-House-Platform/blob/4c426a1d/docs/ci/optimization-plan.md) · [All PR commits](https://github.com/egawilldoit/Ega-House-Platform/pull/341/commits) · [Exact post-merge workflow](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41526874883) · [Strict aggregate gate source](https://github.com/egawilldoit/Ega-House-Platform/blob/4c426a1d/scripts/ci/aggregate-gate.mjs) · [Release helper source](https://github.com/egawilldoit/Ega-House-Platform/blob/4c426a1d/scripts/ci/release-evidence.mjs) · [GitHub official concurrency](https://docs.github.com/en/actions/how-tos/write-workflows/choose-when-workflows-run/control-workflow-concurrency) · [GitHub official attempt jobs API](https://docs.github.com/en/rest/actions/workflow-jobs).

---

## 16. What to do next

1. **Before a mobile release:** run the strict release-evidence helper on the exact intended `main` SHA. Use a new `force_full` run only if existing full evidence cannot be accepted; never publish on a PR or partial run.
2. **Performance evidence:** gather ≥10 ordinary, fully executed, comparable CI workflows; calculate scoped median/p90 and runner minutes without cherry-picking a best run. Track `db-invariants`, web/build, shard balance and hygiene overhead.
3. **Main concurrency:** optionally stage an authorized, safe double-push rehearsal to confirm that both main run IDs complete.
4. **Separate runtime work:** continue production deployment-SHA verification, API region investigation, authenticated E2E and PR #337 Active CPU measurement independently. Do not mark these done on the basis of this CI merge.

**Authoritative links:** [PR #341](https://github.com/egawilldoit/Ega-House-Platform/pull/341) · [Merged main run](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41526874883) · [Audit](https://github.com/egawilldoit/Ega-House-Platform/blob/4c426a1d/docs/ci/performance-audit.md) · [Plan](https://github.com/egawilldoit/Ega-House-Platform/blob/4c426a1d/docs/ci/optimization-plan.md) · [Unified workflow](https://github.com/egawilldoit/Ega-House-Platform/blob/4c426a1d/.github/workflows/unified-platform-validation.yml) · [Aggregate](https://github.com/egawilldoit/Ega-House-Platform/blob/4c426a1d/scripts/ci/aggregate-gate.mjs) · [Release evidence](https://github.com/egawilldoit/Ega-House-Platform/blob/4c426a1d/scripts/ci/release-evidence.mjs) · [GitHub concurrency docs](https://docs.github.com/en/actions/how-tos/write-workflows/choose-when-workflows-run/control-workflow-concurrency) · [GitHub jobs API](https://docs.github.com/en/rest/actions/workflow-jobs).
