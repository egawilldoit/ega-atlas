---
type: project
status: active
updated: 2026-10-23
related: [gymtrack, agent-delivery-workflow]
---

# Helm

Owner-facing control plane that connects Linear objectives/issues to native T3 Code execution, worktrees, thread follow-ups, and review evidence. It coordinates T3; it is not a second execution engine.

## Current state

- [EGA-BUILDS/helm](https://github.com/EGA-BUILDS/helm): v0.2 integration on `develop` (observed head `b85a8e3` on 2026-10-23). Production `main` remains v0.1. Do not infer Production v0.2 from DEV code or ready builds.
- Native T3 launch/follow-up/interrupt were exercised in a disposable isolated DEV probe; full hosted acceptance and end-to-end workflow are not yet certified. [EGA-758](https://linear.app/egawilldoit/issue/EGA-758) is In Review, not an approved release.
- Live Linear OAuth [EGA-759](https://linear.app/egawilldoit/issue/EGA-759), project mappings [EGA-760](https://linear.app/egawilldoit/issue/EGA-760), editable issue execution [EGA-761](https://linear.app/egawilldoit/issue/EGA-761), and operating-consent UI [EGA-769](https://linear.app/egawilldoit/issue/EGA-769) are In Review. Existing ExecutePanel is not yet connected to the chosen Linear issue workflow.
- As of 2026-10-23, [PR #35](https://github.com/EGA-BUILDS/helm/pull/35) (native write destination allowlist), [PR #36](https://github.com/EGA-BUILDS/helm/pull/36) (mount issue execution review), and [PR #37](https://github.com/EGA-BUILDS/helm/pull/37) (state/docs and connection label) are **open drafts targeting develop**, not merged behavior. The generic starter README on develop is being fixed in PR #37; consult that PR for the proposed operational guide.
- Hosted DEV environment [EGA-770](https://linear.app/egawilldoit/issue/EGA-770) and full journey acceptance [EGA-685](https://linear.app/egawilldoit/issue/EGA-685) remain gating. [EGA-768](https://linear.app/egawilldoit/issue/EGA-768) holds Production promotion.

## Decisions

- **Linear** owns issue requirements/status; **T3 Code** performs actual native work; Git/CI and browser/runtime receipts provide evidence. Helm observes/coordinates; no shadow ticket system or replacement executor.
- Choose a main model for each execution; default **no subagents**. Opt-in subagent/model is a preference input, not a guarantee that T3 orchestrates them.
- Use a dedicated worktree and explicit base/target branch. Let execution create a PR but **do not auto-merge**. Follow-ups, interrupt and attention states stay on the native thread.
- Owner-controlled permissions; separate DEV operate consent from existing Production read-only access; no production release without explicit approval.

## Open questions

- What remains to pass genuine Linear OAuth, mapping and owner-browser acceptance (not just API-key proof)?
- Which hosted DEV prerequisites, consent UI and complete Linear → T3 → PR evidence are still missing? Confirm via EGA-770/769/685.

## Next steps

1. Independently review PRs #35–#37 on their exact heads; resolve blockers and merge only with current project authorization. Preserve fail-closed write destination checks.
2. Complete EGA-759 OAuth and EGA-760 mapping in isolated DEV; certify the proposed PR #36 ExecutePanel wiring with a real selected Linear issue.
3. Certify EGA-769/770 consent and hosted DEV isolation; keep Production unchanged.
4. Execute EGA-685 full workflow and failure-path review before proposing a release.

## Links

- [Source code](https://github.com/EGA-BUILDS/helm)
- [EGA-685 acceptance issue](https://linear.app/egawilldoit/issue/EGA-685)
- [Agent delivery workflow](../concepts/agent-delivery-workflow.md), [Gymtrack](gymtrack.md)
