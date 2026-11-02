---
type: project
status: active
updated: 2026-11-02
related: [agent-delivery-workflow, helm]
---

# EGA House

Personal productivity platform (Project → Goal → Task → Timer → Review), with web/mobile clients, standalone API and agent integrations. W0–W3/shell are merged, as is [CI PR #341](https://github.com/egawilldoit/Ega-House-Platform/pull/341), with the exact post-merge main CI successful. Authenticated E2E and measured Vercel Active CPU savings remain unverified.

## Current state

- [Ega-House-Platform](https://github.com/egawilldoit/Ega-House-Platform) `main@4c426a1d0a6be1d2e22b34860ba96db7236b9001`, verified 2026-11-02. [PR #341](https://github.com/egawilldoit/Ega-House-Platform/pull/341) merged at 21:16 UTC; [post-merge main CI](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41526874883) **success: 190s, 17 successful jobs and 1 informational skip**. [Full CI/CD delivery report, results and 43-technique audit](../../raw/ega-house/2026-11-02-ci-cd-performance-and-release-safety.md). [PR #339](https://github.com/egawilldoit/Ega-House-Platform/pull/339) was already merged, with separate manual acceptance/E2E boundaries.
- Linear [W0 EGA-771](https://linear.app/egawilldoit/issue/EGA-771), [W1 EGA-772](https://linear.app/egawilldoit/issue/EGA-772), [W2 EGA-773](https://linear.app/egawilldoit/issue/EGA-773) and [W3 EGA-774](https://linear.app/egawilldoit/issue/EGA-774) all read **Done** (latest updates 2026-10-27–29). Corresponding feature PRs are merged into `main`, including [Areas #328](https://github.com/egawilldoit/Ega-House-Platform/pull/328) and [Home #333](https://github.com/egawilldoit/Ega-House-Platform/pull/333). Delivered code includes task interactions, saved views/table/calendar/timeline, favourites, notifications, archive, agent attribution, areas, project files, estimates, templates and Home improvements. These issue closures do **not** independently certify deployed migrations, browser flows or Production acceptance.
- [PR #336](https://github.com/egawilldoit/Ega-House-Platform/pull/336) merged 2026-10-30: authenticated workspace UI/motion polish, without schema/API/auth changes. Its report records typecheck, build and 2,257 web tests passed, but browser screenshots and accessibility inspection were **skipped**; do not treat them as passed.
- [PR #338](https://github.com/egawilldoit/Ega-House-Platform/pull/338) merged 2026-10-30: configured web functions for `dub1`. **Independent Vercel verification on 2026-11-01:** Production web READY, aliased at `www.egawilldoit.online`, source `main@6b8d709`, deployment region `dub1`. Production API/server also READY and aliased at `ega-api.egawilldoit.online` at the same SHA, but its deployment region remains **`iad1` (US East)**. Dublin is proven for web deployment metadata, **not API co-location**. Actual function execution traces, latency and Active CPU measurements were not tested.
- [PR #337](https://github.com/egawilldoit/Ega-House-Platform/pull/337) remains **open**: realtime visibility refresh correctness and narrower sidebar payload. Its own report says no measured Active CPU reduction; the 50% reduction is still a target, not an achieved result. The PR reported a shared `decode-uri-component` security-audit blocker on 2026-10-30; recheck current CI before action.
- [PR #339](https://github.com/egawilldoit/Ega-House-Platform/pull/339) **merged** 2026-11-01: shared `(workspace)` layout keeps sidebar/top bar mounted across Home, Today, Tasks, Goals, Timer and Review; route loading states are content-scoped. Repairs restored moved imports, duplicate-route/build failures and existing page behavior; architecture/security proof paths follow the migrated routes. The owner subsequently said the manual check was “all good”; this is owner-reported acceptance, not an independently witnessed test-by-test result.
- Earlier #339 made package/regression/lint jobs change-aware; its 331→285s report is separate from #341. **PR #341 shipped:** strict `hygiene`, exact-SHA/run/attempt mobile release evidence, safe main concurrency, `.npmrc` filters, parallel web build/Playwright/typecheck and two complete Vitest shards (306 files/2313 tests). Measured full-matrix baseline **346s**, optimized runs **141/172/250/190s** (four-run descriptive median **181s, −47.7%**); runner-minutes rose on average. [Detailed record](../../raw/ega-house/2026-11-02-ci-cd-performance-and-release-safety.md).
- Verification boundaries: the [PR report](https://github.com/egawilldoit/Ega-House-Platform/pull/339) records typecheck/build, 2,284 web tests and two visual shell contracts passed. Authenticated shell-persistence E2E did not run; drawer/timer cases remain unconditional skips. E2E work was explicitly excluded from this delivery. Production-default test targets and weak shortcut/document-navigation assertions remain unresolved. The database seed URL is required for notification seeding, not merely creating the navigation fixture.
- Autonomous Runner integration is separate from Helm's native T3 control plane. No end-to-end Production delivery acceptance is inferred from merged features.

## Decisions

- Gr8r remains an **observable UX reference only**; no copying unlicensed JS, HTML, CSS, assets or state logic. Reuse EGA domain/actions and existing architecture.
- Keep UI delivery, CI, schema/migration application, Vercel deployment, browser acceptance and Production acceptance as separate gates.
- Keep the 50% Vercel Active CPU objective measurement-led; GitHub Actions speed is unrelated to proved app CPU savings.
- For APK/OTA, accept only the complete exact-main-SHA trusted run+attempt; test the helper before release, and avoid a redundant force-full dispatch if the successful existing main push qualifies.
- Runner and Helm remain separate; no automatic merges or architectural replacement without explicit authorization.

## Open questions

- Web and API Vercel Production SHAs were verified at `6b8d709` on 2026-11-01; after #341 merged to `4c426a1`, what SHA is now actually deployed and aliased, what migrations are live, and what browser journeys are accepted?
- Why does the Production API/server still report `iad1` while web reports `dub1`? Confirm actual execution regions and database-to-API latency before changing configuration.
- When E2E work is explicitly resumed, can authenticated shell persistence, drawer/timer behavior and owner isolation be proved on a nonproduction target? Green CI and owner-reported manual acceptance do not establish these automated results.
- What is the measured Vercel Active CPU baseline, and what does #337 change after a comparable observation window?
- Will the strict release-evidence CLI accept the full main run #41526874883 before any APK/OTA? A stable 10-run CI performance p50/p90 also remains pending.

## Next steps

0. Before any APK/OTA, verify exact SHA `4c426a1` with `release-evidence.mjs`; dispatch force-full only if needed. Collect ≥10 comparable full-matrix CI runs before promoting a stable median claim.

1. Keep #339 recorded as shipped and owner-reported manually accepted; retain its E2E gaps without reopening excluded test work automatically.
2. Recheck [#337](https://github.com/egawilldoit/Ega-House-Platform/pull/337) CI/security audit and measure CPU before claiming savings or planning more optimization.
3. Investigate API `iad1` versus web `dub1` deployment region and documented database location; collect comparable function/DB latency and CPU evidence before proposing an authorized deployment change.
4. Record owner/browser acceptance separately for W0–W3, including any outstanding migration and accessibility evidence.

## Links

- [Repository](https://github.com/egawilldoit/Ega-House-Platform) · [W0 scope](https://linear.app/egawilldoit/issue/EGA-771) · [Shell PR #339](https://github.com/egawilldoit/Ega-House-Platform/pull/339) · [CI PR #341](https://github.com/egawilldoit/Ega-House-Platform/pull/341)
- [Complete CI optimization and release-safety report](../../raw/ega-house/2026-11-02-ci-cd-performance-and-release-safety.md)
- [Agent delivery workflow](../concepts/agent-delivery-workflow.md) · [Helm](helm.md)
