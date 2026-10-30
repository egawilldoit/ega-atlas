---
type: decision
status: accepted
updated: 2026-10-30
related: [gymtrack]
---

# 0004 Gymtrack Sprint 2 acceptance boundary

Close Sprint 2 by owner acceptance of delivered implementation, with unfinished verification and deletion explicitly deferred.

## Context

The main onboarding and staff/RBAC journey worked in manual DEV testing after repairs. Full negative, recovery and regression acceptance remained incomplete. The owner explicitly chose to stop Sprint 2 checking and carry remaining work forward.

## Options

1. Keep Sprint 2 open until every original acceptance criterion passes.
2. Close accepted delivery scope and preserve remaining work as explicit Sprint 3 debt.

## Decision

Choose option 2. [GYM-71](https://linear.app/egawilldoit/issue/GYM-71) is Done. [GYM-88](https://linear.app/egawilldoit/issue/GYM-88) is reopened for deferred Sprint 3 verification. [GYM-75](https://linear.app/egawilldoit/issue/GYM-75) and [GYM-86](https://linear.app/egawilldoit/issue/GYM-86) move out of the sprint while retaining unfinished states.

## Consequences

- Original full-certification closure criteria are superseded for sprint tracking by explicit owner scope acceptance.
- Unexecuted tests remain unexecuted; screenshots and owner confirmations retain their evidence limits.
- Sprint closure grants no production approval and proves no earlier affected-account recovery.
- Original acceptance details remain in Linear; Atlas summarizes the delivery rather than duplicating the test backlog.

## Links

- [Gymtrack delivery report](../projects/gymtrack.md)
- [Owner acceptance recorded in GYM-71](https://linear.app/egawilldoit/issue/GYM-71)
