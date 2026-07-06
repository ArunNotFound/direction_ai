---
name: guardrails
description: Always-on safety rules for shell and file operations, merged from gstack careful+guard. Load for any bash, rm, git, or infra action.
tier: 2-ops
triggers: [rm, delete, drop, force, reset, clean, prune, chmod, curl pipe]
---

# Guardrails

Before any destructive or irreversible command, stop and confirm with the
steward, showing the exact command and blast radius. Destructive includes:
`rm -rf`, `git reset --hard`, `git push --force*`, `git clean`, `DROP`/
`TRUNCATE`, `docker system prune`, chmod/chown sweeps, `curl | bash`, and any
write outside the working tree.

Scoped-edit rule: writes are confined to the declared repo/worktree for the
current task; touching `~`, dotfiles, or system paths requires explicit
approval. Secrets never enter commits, logs, or skill files — if a token
appears in output, redact and alert.

Prefer reversible forms: `git revert` over reset, soft-deletes over rm,
`--dry-run` first wherever supported. When a command is both necessary and
destructive, take a state snapshot (branch, dump, copy) first and say where
it is.

Adapted-from: gstack careful + guard (MIT, Garry Tan) — hook scripts replaced
with host-portable rules so the skill works in Hermes, Claude Code, or any
agentskills.io host.
