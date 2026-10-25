---
type: project
status: active
updated: 2026-10-25
related: [agent-delivery-workflow, helm]
---

# EGA House

Personal productivity platform (Project → Goal → Task → Timer → Review), with web/mobile clients, standalone API and agent integrations. **UX upgrade planning resumed**; delivery is not yet in the top-three `now.md` queue.

## Current state

- [Ega-House-Platform](https://github.com/egawilldoit/Ega-House-Platform) `main@d8bc7d4ea3b7871cf9aa6674305e4cb0c56e1aab` (last commit 2026-10-17, rechecked 2026-10-25). Existing npm workspace uses Next.js/React, Expo, Hono and shared domain/data packages. No newly merged UX-upgrade PR was found in the checked 2026-10-22 onward window.
- Linear now scopes a **Gr8r-inspired, clean-room** task-workspace upgrade: [W0 EGA-771](https://linear.app/egawilldoit/issue/EGA-771) build/reuse baseline, [W1 EGA-772](https://linear.app/egawilldoit/issue/EGA-772) task workspace, [W2 EGA-773](https://linear.app/egawilldoit/issue/EGA-773) views/navigation and [W3 EGA-774](https://linear.app/egawilldoit/issue/EGA-774) EGA-native extensions. All four were **Todo** at this check; planning is not implementation or deployment.
- W0's dated 2026-10-24 audit of the unchanged `main` recorded typechecks and architecture check passing, 1,786 web tests passing, 40 ESLint warnings, and a sandbox `next/font/google` fetch/build failure. These are **issue-recorded audit results**, not tests rerun here. [EGA-778](https://linear.app/egawilldoit/issue/EGA-778) tracks the font fix.
- Existing autonomous Runner is partially integrated, **not** a proven Production delivery system. It is distinct from Helm's native T3 control plane.

## Decisions

- Gr8r is an **observable UX reference only**; its repository has no reusable license. Do not copy its JS, HTML, CSS, assets, seed data or state/store logic. Reimplement within EGA's existing stack and reuse existing actions, components and domain contracts.
- Sequence W0 build health and shared primitives before W1/W2/W3. No duplicate services or speculative new design system.
- Existing domain/application/data layers own rules; agent Runner merge policy stays human review unless separately authorized. Do not merge Runner and Helm architectures by inference.

## Open questions

- When should the owner promote this planned backlog into the delivery queue relative to job search, Gymtrack and Helm? Planning activity alone does not supersede `now.md` priorities.
- Which exact W0 build issues reproduce outside the restricted sandbox, and which current components can be reused before frontend work?
- Which Runner workflow, if any, is independently runtime-accepted at the latest code SHA?

## Next steps

1. Keep W0–W3 as a scoped, **unimplemented** backlog until an explicit delivery slot is assigned.
2. When scheduled, start with [EGA-778](https://linear.app/egawilldoit/issue/EGA-778) and [EGA-771](https://linear.app/egawilldoit/issue/EGA-771), verifying the latest `main`, repository `AGENTS.md` and reuse contract before code changes.
3. Preserve clean-room and test/CI boundaries; do not expand into Runner replacement or unrelated features.

## Links

- [Repository](https://github.com/egawilldoit/Ega-House-Platform) · [W0 source contract](https://linear.app/egawilldoit/issue/EGA-771)
- [Agent delivery workflow](../concepts/agent-delivery-workflow.md) · [Helm](helm.md)
