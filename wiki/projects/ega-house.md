---
type: project
status: parked
updated: 2026-10-23
related: [agent-delivery-workflow, helm]
---

# EGA House

Personal productivity platform (Project → Goal → Task → Timer → Review), with web/mobile clients, standalone API and agent integrations. Context preserved; not a current `now.md` delivery priority.

## Current state

- [Ega-House-Platform](https://github.com/egawilldoit/Ega-House-Platform) is an npm-workspace monorepo with Next.js web, Expo mobile, Hono server, shared domain/data packages and a partially integrated autonomous Runner.
- The repository explicitly says the Runner is **not** proved as a full end-to-end Production delivery system. Read its `ARCHITECTURE.md` and `CONTEXT.md` for actual code/current behavior.
- **Overlap boundary:** EGA House's partial autonomous Runner (Linear/queue/Hermes/GitHub work) and Helm's native T3 control plane are different workstreams. Do not connect or replace one with the other without a separate owner decision and proven need.
- Parked means no new work is allocated ahead of job search, Gymtrack, Helm and EGA Skills unless the owner resumes it.

## Decisions

- Shared domain/application/data layers own product rules; preserve repository architecture boundaries.
- Runtime evidence must confirm the real user workflow; a green build or agent claim is not equivalent to Product acceptance.
- Runner merge policy remains human review unless explicitly authorized otherwise.

## Open questions

- Which EGA House outcome, if any, should be resumed after the current priority queue? Its parked state is a priority decision, not a claim that no code exists.
- Is the partial Runner still necessary alongside Helm, or should further autonomous delivery work remain deferred? Do not infer an architecture merger.
- What Runner vertical slice is actually runtime-accepted at the newest code SHA? Recheck before any implementation.

## Next steps

1. No new feature work while parked.
2. If resumed: read `AGENTS.md`, `CONTEXT.md` and `ARCHITECTURE.md`, then select one bounded next outcome.

## Links

- [Repository](https://github.com/egawilldoit/Ega-House-Platform)
- [Agent delivery workflow](../concepts/agent-delivery-workflow.md), [Helm](helm.md)
