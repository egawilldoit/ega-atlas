---
type: decision
status: accepted
updated: 2026-10-23
related: [gymtrack, agent-delivery-workflow]
---

# 0003 Guard Gymtrack shared DEV migrations

Use one shared Supabase DEV with a serialized, verifiable migration workflow instead of unmanaged schema experiments.

## Context

Three developers share one DEV database while feature branches can be ahead of the protected integration branch. Concurrent applied-but-unmerged SQL, unreconciled schema drift or privileged runtime access can invalidate another developer's tests.

## Options

1. Let independent feature branches apply schema migrations concurrently.
2. Serialize shared DEV applications with reviewed SQL, authorized operator, locked apply, exact receipts and forward-fix recovery.
3. Require each developer to run a separate local database for normal application work.

## Decision

**Option 2.** One applied-but-unmerged schema candidate at a time. Review/commit/push SQL and the app candidate; inspect exact-head CI and plan; apply only approved pending versions through guarded `APPLY DEV`, then preserve independent checksummed receipts. Local app testing can target shared DEV through restricted runtime credentials; migration credentials remain isolated.

## Consequences

- Source of schema history is versioned `supabase/migrations` and registered catalogs, **not** Drizzle schema definitions.
- Do not reset shared DEV, modify/replay already-applied SQL, hide drift or silently combine conflicting migration operators. Use new forward migrations and reconcile exact version/checksum lineage to the eventual merge.
- Restricted runtime/RLS/database readiness is necessary but **not equivalent** to real Auth, mail, browser or two-tenant functional acceptance.
- Only the current GitLab migration guide and approved delivery procedures authorize operational steps; this Atlas decision summarizes the rationale, not credential or migration commands.

## Links

- [Gymtrack](../projects/gymtrack.md), [Agent delivery workflow](../concepts/agent-delivery-workflow.md)
- [Canonical migration procedure (private GitLab)](https://gitlab.com/trackgym101/gymsas/-/blob/develop/docs/database-migrations.md)
- [GYM-88 DEV delivery and acceptance](https://linear.app/egawilldoit/issue/GYM-88)
