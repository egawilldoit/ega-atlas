---
type: project
status: active
updated: 2026-11-01
related: [agent-delivery-workflow, helm]
---

# EGA House

Personal productivity platform (Project → Goal → Task → Timer → Review), with web/mobile clients, standalone API and agent integrations. The W0–W3 workspace upgrade and persistent workspace shell are merged. The owner reports manual acceptance of the shipped shell; automated authenticated E2E and measured CPU savings remain unverified.

## Current state

- Canonical [Ega-House-Platform](https://github.com/egawilldoit/Ega-House-Platform) `main@6b8d70921736167a89091a18fec52a9e9eca925a`, checked 2026-11-01: [PR #339](https://github.com/egawilldoit/Ega-House-Platform/pull/339) merged at 19:54 UTC. [Post-merge main CI](https://github.com/egawilldoit/Ega-House-Platform/actions/runs/41344085379) succeeded on this exact SHA.
- Linear [W0 EGA-771](https://linear.app/egawilldoit/issue/EGA-771), [W1 EGA-772](https://linear.app/egawilldoit/issue/EGA-772), [W2 EGA-773](https://linear.app/egawilldoit/issue/EGA-773) and [W3 EGA-774](https://linear.app/egawilldoit/issue/EGA-774) all read **Done** (latest updates 2026-10-27–29). Corresponding feature PRs are merged into `main`, including [Areas #328](https://github.com/egawilldoit/Ega-House-Platform/pull/328) and [Home #333](https://github.com/egawilldoit/Ega-House-Platform/pull/333). Delivered code includes task interactions, saved views/table/calendar/timeline, favourites, notifications, archive, agent attribution, areas, project files, estimates, templates and Home improvements. These issue closures do **not** independently certify deployed migrations, browser flows or Production acceptance.
- [PR #336](https://github.com/egawilldoit/Ega-House-Platform/pull/336) merged 2026-10-30: authenticated workspace UI/motion polish, without schema/API/auth changes. Its report records typecheck, build and 2,257 web tests passed, but browser screenshots and accessibility inspection were **skipped**; do not treat them as passed.
- [PR #338](https://github.com/egawilldoit/Ega-House-Platform/pull/338) merged 2026-10-30: configured web functions for `dub1`. **Independent Vercel verification on 2026-11-01:** Production web READY, aliased at `www.egawilldoit.online`, source `main@6b8d709`, deployment region `dub1`. Production API/server also READY and aliased at `ega-api.egawilldoit.online` at the same SHA, but its deployment region remains **`iad1` (US East)**. Dublin is proven for web deployment metadata, **not API co-location**. Actual function execution traces, latency and Active CPU measurements were not tested.
- [PR #337](https://github.com/egawilldoit/Ega-House-Platform/pull/337) remains **open**: realtime visibility refresh correctness and narrower sidebar payload. Its own report says no measured Active CPU reduction; the 50% reduction is still a target, not an achieved result. The PR reported a shared `decode-uri-component` security-audit blocker on 2026-10-30; recheck current CI before action.
- [PR #339](https://github.com/egawilldoit/Ega-House-Platform/pull/339) **merged** 2026-11-01: shared `(workspace)` layout keeps sidebar/top bar mounted across Home, Today, Tasks, Goals, Timer and Review; route loading states are content-scoped. Repairs restored moved imports, duplicate-route/build failures and existing page behavior; architecture/security proof paths follow the migrated routes. The owner subsequently said the manual check was “all good”; this is owner-reported acceptance, not an independently witnessed test-by-test result.
- CI optimization in #339 folded `delivery-map` into `workspace`, restricted advisory `lint-report` to dispatch/force-full, and made package/regression/lint jobs change-aware while preserving required status names. No tests were deleted. The supplied report compares full runs at 1,104 versus 1,002 job-seconds and 331 versus 285 seconds elapsed; different SHAs mean indicative runtime savings, not a controlled benchmark or measured monetary/Active CPU savings.
- Verification boundaries: the [PR report](https://github.com/egawilldoit/Ega-House-Platform/pull/339) records typecheck/build, 2,284 web tests and two visual shell contracts passed. Authenticated shell-persistence E2E did not run; drawer/timer cases remain unconditional skips. E2E work was explicitly excluded from this delivery. Production-default test targets and weak shortcut/document-navigation assertions remain unresolved. The database seed URL is required for notification seeding, not merely creating the navigation fixture.
- Autonomous Runner integration is separate from Helm's native T3 control plane. No end-to-end Production delivery acceptance is inferred from merged features.

## Decisions

- Gr8r remains an **observable UX reference only**; no copying unlicensed JS, HTML, CSS, assets or state logic. Reuse EGA domain/actions and existing architecture.
- Keep UI delivery, CI, schema/migration application, Vercel deployment, browser acceptance and Production acceptance as separate gates.
- Keep the 50% CPU objective measurement-led; do not equate fewer queries, code changes or READY builds with proven usage reduction.
- Runner and Helm remain separate; no automatic merges or architectural replacement without explicit authorization.

## Open questions

- Web and API Vercel Production deployment SHAs now match `6b8d709`; which database migrations are live, and has the owner accepted the W0–W3 journeys in the browser?
- Why does the Production API/server still report `iad1` while web reports `dub1`? Confirm actual execution regions and database-to-API latency before changing configuration.
- When E2E work is explicitly resumed, can authenticated shell persistence, drawer/timer behavior and owner isolation be proved on a nonproduction target? Green CI and owner-reported manual acceptance do not establish these automated results.
- What is the measured Vercel Active CPU baseline, and what does #337 change after a comparable observation window?

## Next steps

1. Keep #339 recorded as shipped and owner-reported manually accepted; retain its E2E gaps without reopening excluded test work automatically.
2. Recheck [#337](https://github.com/egawilldoit/Ega-House-Platform/pull/337) CI/security audit and measure CPU before claiming savings or planning more optimization.
3. Investigate API `iad1` versus web `dub1` deployment region and documented database location; collect comparable function/DB latency and CPU evidence before proposing an authorized deployment change.
4. Record owner/browser acceptance separately for W0–W3, including any outstanding migration and accessibility evidence.

## Links

- [Repository](https://github.com/egawilldoit/Ega-House-Platform) · [W0 scope](https://linear.app/egawilldoit/issue/EGA-771) · [Shell PR #339](https://github.com/egawilldoit/Ega-House-Platform/pull/339)
- [Agent delivery workflow](../concepts/agent-delivery-workflow.md) · [Helm](helm.md)
