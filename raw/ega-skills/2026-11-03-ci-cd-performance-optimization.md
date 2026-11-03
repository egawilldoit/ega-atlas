# EGA Skills — CI/CD performance optimization: Batch 0–5 delivery record

**Recorded:** 2026-11-03 · **Scope:** `egawilldoit/ega-skills` GitHub Actions performance/reliability, not a product feature release.  
**Outcome:** Batch 0–5 complete; [PR #197](https://github.com/egawilldoit/ega-skills/pull/197) and [PR #198](https://github.com/egawilldoit/ega-skills/pull/198) merged into `main`. Batch 1 merge `64f217b221ef1b1f6de0839d8dca9448c25d2fb0`; Batches 2–5 merge `10139052c3b7c1399886a18ce007279cbeda82a1`.  
**Post-merge CI:** [run 41676267240](https://github.com/egawilldoit/ega-skills/actions/runs/41676267240), exact `10139052c`, SUCCESS, including `ci-gate`, Linux foundation, PostgreSQL isolation and Contract F RLS. Routine `main` pushes intentionally omit Windows.  
**Post-merge live release verification:** [run 41676899300](https://github.com/egawilldoit/ega-skills/actions/runs/41676899300), exact `10139052c`: **IN PROGRESS (not certified)** at the time of this record. This is a **separate** gate from GitHub CI; do not infer production promotion from a green CI run.

This is a public-safe technical summary of merged implementation, measured results and source links. The owning code, tests and detailed measurement workbooks remain in [EGA Skills](https://github.com/egawilldoit/ega-skills); Atlas does not replace them. The [EGA Skills project page](../../wiki/projects/ega-skills.md) is the current-state entrypoint.

## 1. Executive result

| Metric | Historical before work | After Batch 1 | Batches 2–5, measured | Interpretation |
|---|---:|---:|---:|---|
| Ordinary Linux PR CI latency | 360 s / 6m00s median | 294 s / 4m54s | **186 s / 3m06s** on final PR head | About **48.3% shorter** versus historical median |
| Full Linux + Windows CI | 1,056 s / 17m36s median | ~916 s / 15m16s | **489 s / 8m09s** median of five comparable sharded runs | About **53.7% shorter** versus historical median |
| Full CI successful-run range | Windows-enabled baseline four observations | — | **449–622 s** over five identical workflow-code runs | Windows install/runner variance is material |
| Windows foundation critical job | 1,032 s median | 889 s | **416–569 s** critical shard | Separate 95-file parallel jobs, not less test coverage |
| Linux foundation critical job | 258 s median | 270 s | **144–180 s** critical shard | 76+76 core tests and independent non-test checks |
| PR runner consumption | mixed cohorts, not comparable | 9.667 job-min | **9.6–10.1 job-min** | Roughly neutral, not 48% less compute |
| Full runner consumption | mixed cohorts, not comparable | 30.267 job-min | **27.6–33.7 job-min**, median ~30.1 | Roughly neutral, not 54% less compute |
| Test discovery | 189 files (151 core + 38 web) | 189 | **190 files (152 core + 38 web)** | +1 contract-test file; no previous tests removed |

**Methodological boundary:** the original 360/1,056-second medians come from small, mixed-version historical cohorts, while later runs use the optimized workflow. They are descriptive comparisons, not a controlled same-hardware A/B, not a stable p90, and not a claim that throughput/cost fell by the same percentage as latency. The optimized full-CI median uses 449, 489, 505, 522 and 622 seconds; the 489-second final-head run is also the median. One additional optimized post-merge Linux-only run succeeded.

**Business outcome:** faster PR feedback and full release verification, while retaining the same underlying test obligations and required release check names. No paid runner, third-party CI migration, production code/schema change or CI cache was introduced.

## 2. Baseline, sampling and audit corrections (Batch 0)

Baseline repo revision: `e5a3ae793784ff90d9c9f707e1499f4d132650b3`. The read-only audit examined 24 recent `CI` runs, a ~30-day list of 282 fetched runs (GitHub reported 283), detailed steps, test-file reporter attribution, job timing and live branch rulesets. Across 19 recent successful attempts, job-minutes averaged **17.278** and had median **12.383**; these are not workflow wall times. Five of 24 runs were canceled, consuming 21.2 job-minutes. The 30-day latest-attempt lower bound was **6,383.7167 job-minutes over 1,549 jobs**; earlier failed attempts were not included. These observations must not be substituted for billing statements.

Historical successful-path samples: 15 Linux runs, **6.00-minute** median (p90 ~6.33 minutes), and only four Windows-enabled runs, **17.6-minute** median (p90 19.2 minutes). The small Windows cohort mixes workflow revisions. For `main`, the longest job was Linux web (~5.7 min); in the full Windows path it was Windows foundation (~17.2 min). A reference Linux web test step lasted **316 s**: `tests/web/skills.test.mjs` accounted for 247.4 s of attributed parallel-test time. A Windows foundation run spent **967 s** in its unscoped test step over 189 files. The Linux core step was ~246 s, including ~102 s in `tests/router/golden/determinism.test.mjs`.

The 43-technique review was grouped into smart execution (1–9), parallelism (10–19), caching (20–30), runners/checkout (31–35), local feedback (36–39), and release controls (40–43). The [complete original 43-row assessment](https://github.com/egawilldoit/ega-skills/blob/main/docs/ci/performance-audit.md#6-complete-43-technique-assessment) records **ALREADY IMPLEMENTED**, **IMPROVE**, **MISSING**, **NOT APPLICABLE**, **UNSAFE** or **NEEDS EVIDENCE** for each item. Key decisions carried into execution:

| Technique(s) | Decision / outcome |
|---|---|
| #1, #2, #4, #5: change detection, dependency and affected-package filtering | **Keep classifier narrow.** `docs/**` may contain generated catalog content and `tests/web/**` includes real-PostgreSQL RLS; broad skips would create false green releases. |
| #3, #6–10, #15: conditional/parallel jobs and cancellation | **Retain.** Ordinary PRs/main pushes skip Windows; release/manual full runs require it. Superseded ordinary PRs may cancel, release checks must not. |
| #11, #12–14, #19: heavy suites, sharding and concurrency | **Implement measured subset.** Optimize repeated web config first, benchmark effective Node concurrency, then introduce verified two-way Windows and Linux sharding. Avoid unproven weighted scheduling. |
| #16–18, #33–35, #40, #42–43: extra setup, checkout, packaging, release shortcuts | **No change.** Mostly 1–5-second phases or not applicable; uploads and extra dependency edges would erase savings. |
| #20–25, #29–30: caching | **No new cache.** Linux frozen install 3–7 s; Windows pnpm-store cache deserves a separate controlled A/B, not speculative adoption. |
| #26: cached test results | **Unsafe**, not implemented. Reusing results risks hiding shared fixture, artifact or authorization regressions. |
| #31–32: vendor/sizing | **No migration / no paid runner.** Batch 2 measured standard 4-CPU, ~16-GiB hosts and actual Node defaults. |
| #36–39: local feedback | Keep existing bounded targeted test invocations; no extra CI-latency promise from unimplemented local tooling. |
| #41: exact-commit release | Keep source ancestry, source CI gate, Vercel deployment check and live canonical verification as **different evidence layers**. |

Audit corrections included: Windows standard runners are **not 2-vCPU single-concurrency**; Node 24 process-isolated default is roughly `availableParallelism()-1` (3 on sampled 4-CPU workers). Exit 137 alone does not prove an OOM cause. An expected 403 but observed 503 in an authorization test was traced proximally to an unreachable local HTTPS mock; its deeper cause remains unknown. `needs` is nontransitive, and a skipped job can look acceptable to GitHub status checks unless an explicit gate rejects it. Audit documentation was the only Batch 0 change, not an implementation.

Sources: [audit](https://github.com/egawilldoit/ega-skills/blob/main/docs/ci/performance-audit.md), [initial plan](https://github.com/egawilldoit/ega-skills/blob/main/docs/ci/optimization-plan.md), [Batch 0–1 results](https://github.com/egawilldoit/ega-skills/blob/main/docs/ci/optimization-results.md). **Historical source-file headings may retain their pre-merge status; the GitHub PR/merge SHA and this dated record supersede them.**

## 3. Batch 1 — share immutable default test configuration, then harden readiness diagnostics

**Merged PR:** [#197](https://github.com/egawilldoit/ega-skills/pull/197) at `64f217b221ef1b1f6de0839d8dca9448c25d2fb0`, originally `2c1fb6c`, documentation correction `533dc54`, final tested head `9152229a7726ddab8180dcafebd395095ac70e75`.

### Mechanism

Four tests previously constructed fresh committed-artifact `ServerConfig` identities at many call sites, repeatedly triggering validation and separate reader lifecycles:

- `tests/web/skills.test.mjs`
- `tests/web/catalog.test.mjs`
- `tests/web/releases.test.mjs`
- `tests/web/category-browse.test.mjs`

Each test file now reuses **one frozen default config** when called without an override; **any explicit environment override still creates a fresh config**, preserving isolation and the deliberate tamper/error cases. There were **126 statically identified default call sites**, some executed repeatedly inside 114-skill loops. The implementation is test-only; no application registry, release artifact, CI workflow or schema was modified in this step.

Local sequential baseline versus optimized four-file experiment: **522 s → 174 s (−66.6%)**; maximum observed process RSS **2,410 MiB → 1,155 MiB (−52.1%)**. The same **184 tests and 639 assertion call sites** were preserved. Registry reader-handle probe: 0 → 1 after 20 same-config opens → 0 after closure → 1 after reopen. The committed 872-file artifact inventory/digest stayed unchanged. These are local process measurements on a 2-CPU VM, not hosted workflow RAM savings; warm OS caches could favor later runs.

Hosted same-head examples:
- [Linux PR 41503801407](https://github.com/egawilldoit/ega-skills/actions/runs/41503801407): **4m49s**, 9.867 executed job-min.
- [Linux/Windows 41503824320](https://github.com/egawilldoit/ega-skills/actions/runs/41503824320): **14m52s**, 30.433 job-min.
- Linux web integration step **139–141 s** in these runs versus the selected historical 316 s. This is descriptive; versions/cohorts differ.

### Readiness revalidation failure and correction

After the documentation correction, the full run on `533dc54` [41531727578](https://github.com/egawilldoit/ega-skills/actions/runs/41531727578) **failed** in Linux web: `/readyz rejects an artifact path that exists but is a file` waited 20 s for a child server that never answered on port 34220. The focused original suite (18/18) passed locally. The failure mechanism was **not confirmed** because the helper discarded child stderr and did not observe early process termination.

The final Batch 1 fix updated `tests/web/console-server.test.mjs` only: bounded/redacted 8-KiB child stderr, stdout draining, early `error`/`exit`/`close` observation, immediate failure for dead child, abortable 20-second readiness deadline, and reliable child/temp cleanup. Four real-child regressions cover early exit, spawn failure, stalled body and `EADDRINUSE` diagnostics. This is **observability and lifecycle hardening**, not proof that a port collision caused the historical failure; production `/readyz` behavior, 200/503 assertions and security checks remain unchanged.

Patched local web: **796 tests; 787 passed, 9 skipped, 0 failed**. Final-head [PR 41620398214](https://github.com/egawilldoit/ega-skills/actions/runs/41620398214) and [full 41620427092](https://github.com/egawilldoit/ega-skills/actions/runs/41620427092) passed; the latter included Windows foundation and `ci-gate` (~15m17s). [Post-merge main CI 41623549529](https://github.com/egawilldoit/ega-skills/actions/runs/41623549529) passed. **Separate live production verification 41624226642 failed**: its bounded wait expired while the Vercel deployment check `ci-gate` remained pending, and the verifier reported no canonical promotion and no recovery attempt. This is **not** a CI test failure and is not evidence of a successful production release.

## 4. Batch 2 — instrument and benchmark concurrency (Windows only adopted)

**PR #198 implementation commit:** `c487883`; benchmark/report `01716c4`.

`scripts/release/run-tests-bounded.mjs` now records Node version, platform, architecture, available CPU parallelism, physical CPU count, total memory, test isolation, selected concurrency and file/discovery counts. It validates `EGA_TEST_CONCURRENCY`, forwarded `--test-concurrency` and bounded `--shard=i/n` arguments. `.github/workflows/ci.yml` adds validated manual-dispatch concurrency inputs; default Linux core continues using the Node default. Registry cold-import performance stays at `--test-concurrency=1`.

Four full hosted runs on the **same `c487883` source** formed two 3-versus-4 pairs:

| Pair | Linux core 3 → 4 | Windows tests 3 → 4 | Full wall 3 → 4 | CI links |
|---|---:|---:|---:|---|
| 1 | 241 → 237 s | **668 → 639 s** | 819 → 774 s | [3](https://github.com/egawilldoit/ega-skills/actions/runs/41625815207) / [4](https://github.com/egawilldoit/ega-skills/actions/runs/41625826614) |
| 2 | 242 → 220 s | **714 → 677 s** | 837 → 802 s | [3](https://github.com/egawilldoit/ega-skills/actions/runs/41627835693) / [4](https://github.com/egawilldoit/ega-skills/actions/runs/41627843587) |

Hosted runner telemetry: **4 CPUs, ~16 GiB, Node v24.22.1, process isolation, implicit concurrency 3**. Paired test totals were identical at each setting (Linux 1,437 tests, Windows 2,233 at this stage). Concurrency 4 improved Windows test time by ~4.8% across both pairs, so **Windows default explicitly 4**. Linux's smaller/inconsistent speedup was within runner variance; **keep Linux default 3**. The web suite was not retuned. No assertion, timeout or skip was loosened.

## 5. Batch 3 — two safe Windows foundation shards

**PR #198 commits:** `9dd6978` implementation, `05bb054` release-path fixture repair, `e433e7b` ownership-derived expectations, `47b7f08` evidence.

Two independent Windows jobs, `foundation-windows-shard-1` and `foundation-windows-shard-2`, each run the complete unscoped discovery **partitioned** as 95+95 test files (including deliberate Windows web overlap). Both use explicit Windows test concurrency 4. The workflow preserves the required release-check label **`foundation (windows-2022)`** on a **real aggregator job**, not a green no-op.

The test runner builds sorted, index-parity partitions via `scripts/ci/sharding.mjs`; each partition emits its index, total, selection count, full count, SHA-256 selection digest and full-inventory digest as job outputs. The aggregator `scripts/ci/verify-shards.mjs` re-derives expected partitions from a fresh checkout, checks both job results are `success`, verifies distinct indices, exact counts/digests and scope, and rejects missing/duplicate/cancelled/skipped/drifted data. It does not trust a reported count alone. The `ci-gate` also directly depends on both shards and the aggregator: GitHub `needs` is not transitive.

- Full hosted run [41631530042](https://github.com/egawilldoit/ega-skills/actions/runs/41631530042): Windows jobs **373 s and 475 s**, with test steps **262 s and 358 s**; aggregator ~7 s. Full wall **505 s** versus **788 s** mean for the two Batch 2 concurrency-4 single-job runs (**−283 s / −35.9%**, cross-commit descriptive).
- Final-head [full run 41643321651](https://github.com/egawilldoit/ega-skills/actions/runs/41643321651): Windows jobs **449/453 s**; two-way test union **95+95 = 190/190**, full-digest prefix `c9da481f2a7b`, final counts **1,020+1,227 = 2,247 tests**; 0 failures, 17 intentional skips.
- [Windows failure injection 41632958074](https://github.com/egawilldoit/ega-skills/actions/runs/41632958074) on temporary `ffb00bc`: shard 1 failed, shard 2 and Linux passed, **Windows aggregator failed and `ci-gate` failed**. Injection removed; its red run is intended negative evidence.
- An early CI failure [41630489965](https://github.com/egawilldoit/ega-skills/actions/runs/41630489965) exposed a stale release-path fixture after new `needs` jobs. `05bb054` corrected it, rather than relaxing the gate.

No duration-weighted scheduler: Windows shard ratio varied between **1.37, 1.12, 1.24, and 1.02** on different runners for nominally identical content. A maintained weight table would chase noise.

## 6. Batch 4 — Linux core sharding and non-test check separation

**PR #198 commits:** `b8aadd8` implementation, `6e1a63f` evidence.

The old Linux `foundation` was the normal PR critical path. It is now split into:
1. `foundation-checks (ubuntu-latest)`: build, artifact verification, web artifact staging check, catalog freshness, typecheck, token vectors, frozen specs and registry performance.
2. `foundation-linux shard (1/2)` and `foundation-linux shard (2/2)`: **76+76 = 152** core files using `--ci-scope=core --shard=i/2`.
3. `foundation (ubuntu-latest)`: the release-required **real aggregator**, which verifies shard union and separately requires non-test checks to have succeeded.

The final-head Linux aggregator logged **`union=152/152`**, digest prefix `efd86633ce8e`. The actual core test summaries were **634+817 = 1,451 tests** (7 intentional skips; 0 failures). Web console, real PostgreSQL 16 isolation and Contract F RLS remain independent CI jobs and are not replaced with mocks.

Hosted evidence: [Batch 4 PR run 41634926037](https://github.com/egawilldoit/ega-skills/actions/runs/41634926037) and [full 41635001638](https://github.com/egawilldoit/ega-skills/actions/runs/41635001638) passed. Comparable PR execution latency went from **282/286 s to 203 s** (−79 to −83 s); final-head [PR run 41643324749](https://github.com/egawilldoit/ega-skills/actions/runs/41643324749) passed in **189 s** creation-to-completion, **186 s** execution estimate. The stage-specific Linux critical shard measured **144–180 s** across optimized runs.

[Linux failure injection 41636516276](https://github.com/egawilldoit/ega-skills/actions/runs/41636516276) on temporary `e2ce2a7` made Linux shard 1 fail; the Linux aggregator and `ci-gate` failed, while shard 2 and checks passed. The same run also independently reproduced a pre-existing `ega-719` web test hang to the **600-second per-test bound**. That extra flake is not attributed to sharding and was not concealed. Injection changes were subsequently removed.

## 7. CI topology and safety contract after Batch 4

`.github/workflows/ci.yml` continues to select `docs`, `web` or `full`. The narrow classifier rejects uncertain history/renames/deletions into the full path. Normal PRs to `main` use Linux; manual full runs and `release/2.0` events also require Windows. Native Vercel Git deployment and the separate live production verifier remain outside the implementation of these batches.

`ci-gate` uses `if: always()`, with **12 direct `needs` entries** (six original logical jobs, three Linux subjobs and three Windows jobs); it fails closed on unknown mode, missing job, failures, cancellations and unexpected skips. The two foundation aggregator check names remain exact:

- `foundation (ubuntu-latest)` — Linux checks plus two real shards.
- `foundation (windows-2022)` — two real Windows shards.
- `contract-f rls (ubuntu-latest)` — unchanged independent real-PostgreSQL job.

The live `release/2.0` ruleset requires those **three byte-identical contexts**. Unlike `main`, the release branch has required-status rules configured. The main branch requires a PR and resolved review threads but **currently does not require CI status checks**: this pre-existing policy gap is a separate owner decision, not something these optimization PRs altered.

Gate mode checks are strict:
- When Windows **is selected**, both Windows shards and their aggregator must report `success`; otherwise all three must report explicit `skipped`.
- When Linux foundation **is selected**, checks and both Linux shards must report `success`; docs-only mode must report them `skipped`.
- The Windows-selection predicate is derived independently in classifier and gate; disagreement fails the gate.
- The aggregators independently re-derive inventory from checked-in `scripts/ci/test-ownership.json` and `scripts/ci/ownership.mjs` and compare SHA-256 digests. Final discovery **190 files = 152 core + 38 web**. Every discovered file is assigned once and no test is removed.

Mandatory independent coverage retained: eight `.rls.mjs` suites outside default Node discovery, real PostgreSQL/`pg_cron` tests, authorization and tenant/workspace isolation, committed artifact/corpus and source-fixture integrity, catalog freshness, both web frontend/server typechecks, source proposal validation, frozen specs, registry cold-import performance pinned to one concurrent test, and Windows web overlap. Two live failure injections plus unit tests demonstrate red propagation; exact release PR-to-`release/2.0` branch behavior was **not** independently staged and remains a pre-release acceptance item.

Implementation source links: [workflow](https://github.com/egawilldoit/ega-skills/blob/main/.github/workflows/ci.yml), [gate and classification](https://github.com/egawilldoit/ega-skills/blob/main/scripts/ci/classify-changes.mjs), [gate entry point](https://github.com/egawilldoit/ega-skills/blob/main/scripts/ci/check-gate.mjs), [partition module](https://github.com/egawilldoit/ega-skills/blob/main/scripts/ci/sharding.mjs), [aggregator](https://github.com/egawilldoit/ega-skills/blob/main/scripts/ci/verify-shards.mjs), [bounded runner](https://github.com/egawilldoit/ega-skills/blob/main/scripts/release/run-tests-bounded.mjs), [operations](https://github.com/egawilldoit/ega-skills/blob/main/docs/operations/ci-optimization.md).

## 8. Batch 5 — final critical-path audit, deliberately no new implementation

**Disposition: COMPLETE — NO ADDITIONAL CHANGE JUSTIFIED.** The audited PR #198 code diff changed 12 files, about +1,774/−36 lines at final head; no new dependency, application feature, database migration, paid runner or cache was added. More complexity was not automatically considered progress.

| Candidate | Decision | Measured basis / reason |
|---|---|---|
| Split `tests/router/golden/determinism.test.mjs` | **DEFERRED** | ~102 s in the heavy Linux shard, but shares golden-registry setup across determinism scenarios and a warm benchmark; splitting is a separate test-semantics refactor. |
| Move that file to the other shard | **REJECTED** | Simple model makes the receiver ~173 s versus the current ~165 s critical shard. |
| Weighted sharding | **REJECTED** | Windows imbalance is unstable; modeled savings ~13 s Linux or ~24 s Windows do not justify weight-maintenance drift. |
| Windows pnpm-store cache | **DEFERRED, highest remaining opportunity** | Frozen install 51–127 s on measured Windows jobs; potential ~30–40 s reduction on slow critical path. Needs ≥3 comparable runs per arm, integrity proof and rollback. |
| Deduplicate Windows non-test checks | **REJECTED** | ~15 s per shard; negligible wall gain and risks losing cross-platform checks. |
| Concurrency 4 on Linux | **REJECTED** | Paired benefit inconsistent against runner variance. |
| Linux pnpm/Node cache | **REJECTED** | 3–7-second install; restore overhead likely dominates. |
| Inline `classify` | **REJECTED** | ~6 s prefix in exchange for multiple separate setup costs and removal of a single fail-closed classifier. |

One test inventory addition reshuffles index-parity membership, so remeasure shard balance after adding/moving files. No weighted scheduler was installed.

## 9. Final evidence chain, exact SHAs, and costs

| Milestone | Exact SHA / PR | CI evidence |
|---|---|---|
| Audit baseline | `e5a3ae7` | [representative old Linux 41355875171](https://github.com/egawilldoit/ega-skills/actions/runs/41355875171) |
| Batch 1 hosted validation | `2c1fb6c` / #197 | [PR 41503801407](https://github.com/egawilldoit/ega-skills/actions/runs/41503801407), [full 41503824320](https://github.com/egawilldoit/ega-skills/actions/runs/41503824320) |
| Batch 1 final tested head | `9152229a7726ddab8180dcafebd395095ac70e75` / #197 | [PR 41620398214](https://github.com/egawilldoit/ega-skills/actions/runs/41620398214), [full 41620427092](https://github.com/egawilldoit/ega-skills/actions/runs/41620427092) |
| Batch 1 merged | `64f217b221ef1b1f6de0839d8dca9448c25d2fb0` | [main 41623549529](https://github.com/egawilldoit/ega-skills/actions/runs/41623549529) SUCCESS; [live 41624226642](https://github.com/egawilldoit/ega-skills/actions/runs/41624226642) FAILED, deployment gate pending |
| Batch 2 benchmark | `c487883` / #198 | [3a](https://github.com/egawilldoit/ega-skills/actions/runs/41625815207), [4a](https://github.com/egawilldoit/ega-skills/actions/runs/41625826614), [3b](https://github.com/egawilldoit/ega-skills/actions/runs/41627835693), [4b](https://github.com/egawilldoit/ega-skills/actions/runs/41627843587) all green |
| Batch 3 implemented | `05bb054` / #198 | [full 41631530042](https://github.com/egawilldoit/ega-skills/actions/runs/41631530042) SUCCESS; [Windows injection 41632958074](https://github.com/egawilldoit/ega-skills/actions/runs/41632958074) expected RED |
| Batch 4 implemented | `b8aadd8` / #198 | [PR 41634926037](https://github.com/egawilldoit/ega-skills/actions/runs/41634926037), [full 41635001638](https://github.com/egawilldoit/ega-skills/actions/runs/41635001638) SUCCESS; [Linux injection 41636516276](https://github.com/egawilldoit/ega-skills/actions/runs/41636516276) expected RED |
| Final PR #198 head | `59b9db8c6ce9f1e8e54333234f3e46c8e01c2cda` | [PR 41643324749](https://github.com/egawilldoit/ega-skills/actions/runs/41643324749) SUCCESS ~189 s; [full 41643321651](https://github.com/egawilldoit/ega-skills/actions/runs/41643321651) SUCCESS ~490 s |
| Batch 2–5 merged | `10139052c3b7c1399886a18ce007279cbeda82a1` | [main 41676267240](https://github.com/egawilldoit/ega-skills/actions/runs/41676267240) SUCCESS; [live 41676899300](https://github.com/egawilldoit/ega-skills/actions/runs/41676899300) IN PROGRESS (not certified) at capture |

**Cost statement:** The user-facing win is waiting time. GitHub-hosted standard runners are free for this public repository's measured workflow, so direct hosted-minute charge in this context is **$0**. Aggregate job-minutes remain roughly unchanged: routine PR ~9.667 → 9.6–10.1, full ~30.267 → 27.6–33.7. Sharding increases simultaneous job capacity requirements (one full matrix started nine core/shard/aggregate jobs instead of seven); it does **not** certify cost or CPU reductions.

**Measurement rules:** distinguish run creation, latest-attempt start, `ci-gate` completion, job `started_at/completed_at` and total job-minutes. Exclude failed/injected/canceled runs from successful-run medians and distinguish same-workflow trials from version-mixed historical baselines.

## 10. Risks, rollback, and precise next work

1. **Production verification:** Batch 1's live verifier failed due to a Vercel deployment-check `ci-gate` pending for the 15-minute bound, despite GitHub CI success; no canonical promotion/recovery was reported. Confirm [Batch 2–5 live verification](https://github.com/egawilldoit/ega-skills/actions/runs/41676899300) and canonical readiness/source before claiming new production release accepted. Do not conflate an Atlas record with a deployment.
2. **Release branch acceptance:** Run an ordinary authorized release-target PR into `release/2.0` to verify the three required check names genuinely resolve to the new aggregator jobs, not just manual full runs. Do not weaken the ruleset.
3. **Pre-existing flake:** `ega-719` intermittently gets an unreachable local HTTPS mock (403 expected / 503 observed) or stalls to 600-second test timeout; underlying cause unconfirmed. Diagnose separately from the successful sharding work, do not mask with retries/timeout inflation.
4. **Windows install variance and possible cache:** benchmark pnpm-store caching with at least three paired runs per arm plus lockfile/store-integrity assertions, only if owner authorizes and measured overhead still justifies it. Avoid paid provider migration.
5. **Maintenance:** after test-file changes, update ownership inventory, assert 190→new count, verify 2-shard union/digests and runner-minute/balance trends; prevent drift in `ci-gate.needs` and required names. Main branch's missing required `ci-gate` status check is an independent branch-policy risk for owner approval.

**Rollback boundaries:** Remove the Batch 2 explicit Windows `|| '4'` to return to implicit concurrency; Windows-shard rollback centers on `9dd6978` and its related gate/fixture updates; Linux-shard rollback also removes `b8aadd8` and must account for its dependency on the shared partition module. Revert related code, job IDs, tests and gate expectations together, not a single workflow job name. Preserve the release-required status labels. There are no database, schema or secret migrations from these batches.

**Out of scope:** production code performance, catalog content/version changes, a live owner-accepted Console journey, paid CI vendors, Vercel/DB operating costs, automatic approval/merge, and actual production recovery testing. No test suite was intentionally weakened and no production resources were modified by this Atlas documentation.

## 11. Authoritative references

- [PR #197 — merged Batch 1 and diagnostics](https://github.com/egawilldoit/ega-skills/pull/197), [merge commit](https://github.com/egawilldoit/ega-skills/commit/64f217b221ef1b1f6de0839d8dca9448c25d2fb0).
- [PR #198 — merged Batches 2–5](https://github.com/egawilldoit/ega-skills/pull/198), [merge commit](https://github.com/egawilldoit/ega-skills/commit/10139052c3b7c1399886a18ce007279cbeda82a1).
- [Full 43-technique audit](https://github.com/egawilldoit/ega-skills/blob/main/docs/ci/performance-audit.md), [initial optimization plan](https://github.com/egawilldoit/ega-skills/blob/main/docs/ci/optimization-plan.md), [Batch 0–1 measured results](https://github.com/egawilldoit/ega-skills/blob/main/docs/ci/optimization-results.md), [Batches 2–5 measured results](https://github.com/egawilldoit/ega-skills/blob/main/docs/ci/batches-2-5-results.md), [CI operator guide](https://github.com/egawilldoit/ega-skills/blob/main/docs/operations/ci-optimization.md).
- [CI source](https://github.com/egawilldoit/ega-skills/blob/main/.github/workflows/ci.yml), [live-release runbook](https://github.com/egawilldoit/ega-skills/blob/main/docs/operations/AUTOMATIC-RELEASE.md), [production verification workflow](https://github.com/egawilldoit/ega-skills/blob/main/.github/workflows/verify-release.yml).

**Source-of-truth discipline:** This report is a dated evidence snapshot. A later GitHub run, merge, production deployment, or ruleset change supersedes the corresponding operational statement here; inspect live systems before acting.
