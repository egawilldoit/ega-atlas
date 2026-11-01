---
type: project
status: active
updated: 2026-11-01
related: [agent-delivery-workflow, helm]
---

# EGA House

Personal productivity platform (Project → Goal → Task → Timer → Review), with web/mobile clients, standalone API and agent integrations. The W0–W3 workspace upgrade is now implemented and merged; open performance and shell changes are **not** delivered.

## Current state

- Canonical [Ega-House-Platform](https://github.com/egawilldoit/Ega-House-Platform) `main@637cb9aad9a10c604aa1f2612c8ff04b25a79678`, checked 2026-11-01. This is the merge of [PR #338](https://github.com/egawilldoit/Ega-House-Platform/pull/338) on 2026-10-30. GitHub merge state is **code evidence**, not proof that Production serves that SHA.
- Linear [W0 EGA-771](https://linear.app/egawilldoit/issue/EGA-771), [W1 EGA-772](https://linear.app/egawilldoit/issue/EGA-772), [W2 EGA-773](https://linear.app/egawilldoit/issue/EGA-773) and [W3 EGA-774](https://linear.app/egawilldoit/issue/EGA-774) all read **Done** (latest updates 2026-10-27–29). Corresponding feature PRs are merged into `main`, including [Areas #328](https://github.com/egawilldoit/Ega-House-Platform/pull/328) and [Home #333](https://github.com/egawilldoit/Ega-House-Platform/pull/333). Delivered code includes task interactions, saved views/table/calendar/timeline, favourites, notifications, archive, agent attribution, areas, project files, estimates, templates and Home improvements. These issue closures do **not** independently certify deployed migrations, browser flows or Production acceptance.
- [PR #336](https://github.com/egawilldoit/Ega-House-Platform/pull/336) merged 2026-10-30: authenticated workspace UI/motion polish, without schema/API/auth changes. Its report records typecheck, build and 2,257 web tests passed, but browser screenshots and accessibility inspection were **skipped**; do not treat them as passed.
- [PR #338](https://github.com/egawilldoit/Ega-House-Platform/pull/338) merged 2026-10-30: Vercel function region configured to `dub1` to align with the Ireland database. Actual Production region, served deployment SHA and post-merge authenticated smoke/latency checks **not verified here**.
- [PR #337](https://github.com/egawilldoit/Ega-House-Platform/pull/337) remains **open**: realtime visibility refresh correctness and narrower sidebar payload. Its own report says no measured Active CPU reduction; the 50% reduction is still a target, not an achieved result. The PR reported a shared `decode-uri-component` security-audit blocker on 2026-10-30; recheck current CI before action.
- [PR #339](https://github.com/egawilldoit/Ega-House-Platform/pull/339) remains **open** as of 2026-11-01: persistent authenticated workspace shell and route-group migration. Review repairs are described in the PR; exact-head CI, browser navigation/E2E behavior and merge readiness require fresh verification. Not merged or deployed.
- Autonomous Runner integration is separate from Helm's native T3 control plane. No end-to-end Production delivery acceptance is inferred from merged features.

## Decisions

- Gr8r remains an **observable UX reference only**; no copying unlicensed JS, HTML, CSS, assets or state logic. Reuse EGA domain/actions and existing architecture.
- Keep UI delivery, CI, schema/migration application, Vercel deployment, browser acceptance and Production acceptance as separate gates.
- Keep the 50% CPU objective measurement-led; do not equate fewer queries, code changes or READY builds with proven usage reduction.
- Runner and Helm remain separate; no automatic merges or architectural replacement without explicit authorization.

## Open questions

- Which exact Production web/API deployment SHAs and database migrations are live, and has the owner accepted the W0–W3 journeys in the browser?
- Did the #338 region configuration actually move Production functions to Dublin, and what are comparable navigation/latency measurements?
- Does #339 preserve all mainline route behavior and pass exact-head CI/E2E after the latest review repairs?
- What is the measured Vercel Active CPU baseline, and what does #337 change after a comparable observation window?

## Next steps

1. Finish [#339](https://github.com/egawilldoit/Ega-House-Platform/pull/339) review: verify current head, route parity, authenticated navigation and exact-head CI/E2E; do not merge a broken shell.
2. Recheck [#337](https://github.com/egawilldoit/Ega-House-Platform/pull/337) CI/security audit and measure CPU before claiming savings or planning more optimization.
3. Verify #338 Production region/deployment identity and run the documented authenticated smoke/latency checks; distinguish configuration merge from live acceptance.
4. Record owner/browser acceptance separately for W0–W3, including any outstanding migration and accessibility evidence.

## Links

- [Repository](https://github.com/egawilldoit/Ega-House-Platform) · [W0 scope](https://linear.app/egawilldoit/issue/EGA-771) · [Shell PR #339](https://github.com/egawilldoit/Ega-House-Platform/pull/339)
- [Agent delivery workflow](../concepts/agent-delivery-workflow.md) · [Helm](helm.md)
