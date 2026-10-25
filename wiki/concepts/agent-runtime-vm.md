---
type: concept
status: active
updated: 2026-10-25
related: [gymtrack, helm, ega-skills, ega-house, agent-delivery-workflow, engineering-portfolio]
---

# Agent runtime VM

One shared Ubuntu machine supports coding-agent sessions, native T3/OpenCode workflows and multiple EGA project checkouts. This is a reusable operations checklist, not a credentials inventory.

## How we use it

- Identify the machine, repository and current working tree before a command. Recheck `git status`, branch, remote and active agents; fetch/pull the correct integration branch before work. Separate repositories and account identities explicitly.
- On the VM, the `heavy -- <command>` wrapper was locally validated for guarding concurrent installs/builds/heavy checks (2026-10-22). Use it when available; it **serializes resource-intensive jobs, not accelerates them**. Verify presence before relying on it.
- Node uses `nvm`. A 2026-10-24 owner execution report records an additive install of `v24.22.1`, global CLI parity and an updated default alias, replacing the earlier observed `v24.18.0` default. This is **reported**, not a live VM recheck; verify active shell/service Node versions and each repository's toolchain contract before acting.
- Canonical source for the custom VM **`update`** wrapper is [VM Updates Scripts](https://github.com/egawilldoit/vm-updates-scripts) (`scripts/update.src.sh`), not the installed `~/bin/update` runtime copy. That repository's README covers its locking, preflight and nonmutating `--verify`/`--dry-run` options. Verify the active installed version before relying on its source contract.
- Record only whether credentials are configured, where the **approved** secret store is, and how to verify authentication without exposing values. Shell `export` is session-scoped; do not assume it persists for other agents or restarts.

## Gotchas and lessons

- **Disk cleanup is destructive.** Audit caches, temp data and stale worktrees first. Never remove the entire `.t3`, `.hermes`, `.codex` or `.local` trees or use blind recursive deletion. Check worktree ownership and pending changes before removing any directory.
- Keep separate live and disposable T3 targets; don't silently reuse an OAuth grant, token, server port or write destination. The presence of a token is not proof that the target is authorized.
- Updating T3 can reconcile/restart its service and interrupt active T3 threads. Update only during an authorized maintenance window after verifying active work, then check CLI, service/process version and health agree. Do not blindly kill processes or assume a responding port proves the expected executable is running.
- Avoid repeated sleeps/polls or re-running the full test suite after every edit. Use a focused reproducer, gather evidence, then execute the necessary full gate once. Wait/retry only with a bounded reason.
- Never copy secret values, raw authentication diagnostics, machine IPs or personal account details into Atlas or agent transcripts.

## Open questions

- Which exact local runtime state and startup service are active now? Verify on the machine; previous session ports, config and tooling may have changed.
- Has each required CLI authentication been made safely available to noninteractive agent processes? Verify via safe status commands; do not record tokens.

## Links

- [Agent delivery workflow](agent-delivery-workflow.md)
- [Gymtrack](../projects/gymtrack.md), [Helm](../projects/helm.md), [Engineering portfolio](../areas/engineering-portfolio.md)
- [VM Updates Scripts canonical source](https://github.com/egawilldoit/vm-updates-scripts)
