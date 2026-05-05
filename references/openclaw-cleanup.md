# OpenClaw Cleanup

Use this reference when the user asks to clean OpenClaw junk, reduce OpenClaw disk usage, inspect `~/.openclaw`, reset OpenClaw, or uninstall OpenClaw local data.

## First pass

1. Check whether `openclaw` exists:
   - `command -v openclaw`
   - `openclaw --version`
2. Inspect size:
   - `du -sh "$HOME/.openclaw" "$HOME/.openclaw"/* 2>/dev/null | sort -hr | head -n 30`
   - `du -sh "$HOME/.openclaw/workspace"/* 2>/dev/null | sort -hr | head -n 30`
3. Check built-in dry runs:
   - `openclaw reset --dry-run --scope full --non-interactive --yes`
   - `openclaw uninstall --dry-run --workspace --state --service --non-interactive --yes`
4. Recommend backup before state changes:
   - `openclaw backup create`

## Risk levels

Low risk, mode `2` may clean automatically after inspection:

- `~/.openclaw/workspace/tmp`
- `~/.openclaw/npm`
- `~/.openclaw/logs`
- `~/.openclaw/cache`
- `~/.openclaw/debug-proxy`

Medium risk, preview only unless the user explicitly names the target:

- `~/.openclaw/browser`
- `~/.openclaw/chrome-profile`
- `~/.openclaw/workspace/agents`
- `~/.openclaw/workspace/raw`
- `~/.openclaw/workspace/node_modules`
- `~/.openclaw/workspace/data`

High risk, never delete by default:

- `~/.openclaw/workspace/state`
- `~/.openclaw/agents`
- `~/.openclaw/memory`
- `~/.openclaw/plugins`
- `~/.openclaw/skills`
- `~/.openclaw/config`
- `~/.openclaw/backups`

## Built-in command boundaries

- `openclaw reset --dry-run --scope full --non-interactive --yes` is safe for preview.
- `openclaw uninstall --dry-run --workspace --state --service --non-interactive --yes` is safe for preview.
- Do not run real `reset` or `uninstall` unless the user explicitly asks to reset or uninstall OpenClaw.
- Before real reset/uninstall, recommend `openclaw backup create` and report that backups can contain private metadata.

## Running-state protection

If OpenClaw gateway, app, browser profile, or node host appears to be running:

- mode `1`: report and preview normally
- mode `2`: only clean low-risk caches and logs; do not mutate state, workspace, memory, config, profile, plugin, or backup directories
- mode `3`: ignore OpenClaw and continue Downloads-only triage

If a cleanup target might be active, tell the user which service or app should be stopped before applying a larger cleanup.
