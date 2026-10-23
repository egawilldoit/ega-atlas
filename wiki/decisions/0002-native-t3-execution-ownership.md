---
type: decision
status: accepted
updated: 2026-10-23
related: [helm, agent-delivery-workflow]
---

# 0002 Native T3 owns Helm coding execution

Helm coordinates real Linear-to-T3 work. It does not create a replacement coding-agent runtime.

## Context

A delivery-control UI needs to launch, observe, follow up and interrupt agent work while preserving real thread, issue, branch and review evidence. Duplicating the executor would create conflicting ownership and unsafe retries.

## Options

1. Implement an independent Helm execution engine and thread store.
2. Reuse the separate EGA House productivity Runner as the Helm backend.
3. Let native T3 Code execute work in explicit isolated worktrees; have Helm coordinate it and link Linear/PR state.

## Decision

**Option 3**, as approved in the Helm v0.2 product/engineering contract. Linear owns issue state, native T3 owns actual threads and execution, Git/CI own code evidence, and Helm is an owner-facing orchestration UI. Per-execution model selection; no subagents by default. Native opt-in support remains capability-gated, not assumed implemented.

## Consequences

- Verify real native T3 launch/send/interrupt and uncertain-outcome recovery; never emulate unsupported writes.
- Require an explicit owner-granted DEV operate connection bound to the approved endpoint/environment; preserve separate Production read-only access.
- Create a reviewable PR, never auto-merge; model review, code review, merge, DEV acceptance and Production promotion as distinct states.
- The [EGA House](../projects/ega-house.md) Runner remains separate unless a future explicit architectural decision changes ownership. Current hosted DEV acceptance is **not proven** by adopting this decision.

## Links

- [Helm](../projects/helm.md), [Agent delivery workflow](../concepts/agent-delivery-workflow.md)
- [Linear issue EGA-761: execution inputs](https://linear.app/egawilldoit/issue/EGA-761), [EGA-758: native DEV proof](https://linear.app/egawilldoit/issue/EGA-758)
- [Helm repository](https://github.com/EGA-BUILDS/helm)
