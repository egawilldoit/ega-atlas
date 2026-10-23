---
type: concept
status: active
updated: 2026-10-23
related: [gymtrack, helm, ega-skills, ega-house, agent-delivery-workflow]
---

# Agent runtime VM

One shared Ubuntu machine supports coding-agent sessions, native T3/OpenCode workflows and multiple EGA project checkouts. This is a reusable operations checklist, not a credentials inventory.

## How we use it

- Identify the machine, repository and current working tree before a command. Recheck `git status`, branch, remote and active agents; fetch/pull the correct integration branch before work. Separate repositories and account identities explicitly.
- On the VM, the `heavy -- <command>` wrapper was locally validated for guarding concurrent installs/builds/heavy checks (2026-10-22). Use it when available; it **serializes resource-intensive jobs, not accelerates them**. Verify presence before relying on it.
- Node used `nvm` and reported `v24.18.0` on 2026-10-22; this is a dated observation, not a permanent pin for all projects. Follow each repository's toolchain contract.
- Record only whether credentials are configured, where the **approved** secret store is, and how to verify authentication without exposing values. Shell `export` is session-scoped; do not assume it persists for other agents or restarts.

## Gotchas and lessons

- **Disk cleanup is destructive.** Audit caches, temp data and stale worktrees first. Never remove the entire `.t3`, `.hermes`, `.codex` or `.local` trees or use blind recursive deletion. Check worktree ownership and pending changes before removing any directory.
- Keep separate live and disposable T3 targets; don't silently reuse an OAuth grant, token, server port or write destination. The presence of a token is not proof that the target is authorized.
- Avoid repeated sleeps/polls or re-running the full test suite after every edit. Use a focused reproducer, gather evidence, then execute the necessary full gate once. Wait/retry only with a bounded reason.
- Never copy secret values, raw authentication diagnostics, machine IPs or personal account details into Atlas or agent transcripts.

## Open questions

- Which exact local runtime state and startup service are active now? Verify on the machine; previous session ports, config and tooling may have changed.
- Has each required CLI authentication been made safely available to noninteractive agent processes? Verify via safe status commands; do not record tokens.

## Links

- [Agent delivery workflow](agent-delivery-workflow.md)
- [Gymtrack](../projects/gymtrack.md), [Helm](../projects/helm.md)
