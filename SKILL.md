---
name: mac-cleanup
description: "Use when the user wants practical macOS disk cleanup, asks to clean Mac junk such as '帮我清Mac垃圾' or '清理 Mac 垃圾', invokes @mac-cleanup or $mac-cleanup, asks for Mole-assisted cleanup, asks to clean OpenClaw junk, or wants exact-folder triage in Downloads or Movies. Supports three modes: 1 dry-run plus deletion advice, 2 direct conservative cleanup, and 3 Downloads-only triage. Ambiguous activation must show the three-option menu first; explicit mode requests go directly to that mode. Inspect real machine state, preview before destructive commands, back up stateful agent data, avoid blind scans, and verify freed space after changes."
---

# Mac Cleanup

Perform conservative macOS cleanup from real machine facts. Keep the skill compact: inspect first, preview or back up when state matters, delete only low-risk targets automatically, and verify after every mutation.

## Activation

Soft-support these invocations:

- `@mac-cleanup`
- `$mac-cleanup`
- `帮我清Mac垃圾`
- `清理 Mac 垃圾`
- `清 OpenClaw 垃圾`

For ambiguous activation, the first response must show this menu and stop:

1. `先做 dry-run，再给我删除建议`
2. `直接帮我清理 Mac`
3. `看看 Downloads 还能清什么`

Ambiguous activation includes only naming the skill, `@mac-cleanup`, `$mac-cleanup`, `帮我清Mac垃圾`, or `清理 Mac 垃圾` without a mode.

Skip the menu and enter the mode directly when the user clearly says `1`, `2`, `3`, `dry-run`, `直接帮我清理 Mac`, `直接清理`, `直接删`, `看看 Downloads`, or equivalent.

When the user asks to clean OpenClaw junk without a mode, enter mode `1` with the OpenClaw rules from [openclaw-cleanup.md](references/openclaw-cleanup.md).

## Modes

1. `dry-run + deletion advice`
   - Inspect and preview only. Do not delete.
   - Report reclaimable space, low-risk opportunities, and concrete next choices.
2. `direct cleanup`
   - Inspect first, then delete only low-risk targets automatically.
   - User-owned content, app state, agent state, and uninstall/reset actions require explicit scope.
3. `Downloads triage`
   - Inspect `~/Downloads` only.
   - Rank immediate children by size and propose narrowly scoped deletes.

## Safety Rules

- Read [maintenance-principles.md](references/maintenance-principles.md) before touching Codex, OpenClaw, app state, workspaces, sessions, logs, or backups.
- Separate risk levels:
  - Low risk: caches, logs, trash, browser leftovers, package-manager caches, orphaned app leftovers, and aged backup folders with a clear retention rule.
  - Explicit approval required: `~/Downloads`, `~/Movies`, installers, app backups, project artifacts, app uninstall, media folders, business documents, and OpenClaw non-cache state.
  - Do not auto-delete: active project directories, business materials, installed apps, credentials, memory, plugins, config, backups, and ambiguous folders.
- Prefer exact directory coordinates over discovery sweeps. Do not run blind `find` scans across `$HOME` or large trees.
- If a target tool or app is running, report and preview first. Do not mutate its state directories until the user closes it or explicitly accepts the limited low-risk cleanup path.
- Every destructive step must be followed by `df -h` plus targeted `du -sh` verification.

## Default Workflow

1. Resolve the mode or show the menu.
2. Check pressure with `df -h /System/Volumes/Data /`.
3. Inspect obvious buckets: `~/Library/Caches`, `~/Downloads`, `~/Movies`, `~/.Trash`, and any explicitly named tool state.
4. For mode `1`, stop after preview and advice.
5. For mode `2`, delete only low-risk targets, then verify.
6. For mode `3`, inspect only `~/Downloads` immediate children and propose concrete deletions.
7. For OpenClaw, apply [openclaw-cleanup.md](references/openclaw-cleanup.md) and prefer `openclaw backup create` plus dry-run reset/uninstall checks before any state-changing action.

## Mole Procedure

Use this when the user mentions Mole or Mole is the selected tool:

- Read [mole-integration.md](references/mole-integration.md) before recommending or running Mole commands.
- Mole is optional. Detect `mo` locally and do not auto-install it.
- Always run Mole preview commands before real cleanup.
- Do not jump to app uninstall, broad project purge, or system optimization when cache cleanup solves the pressure.

## User Content

For `~/Downloads`, `~/Movies`, and similar folders:

- Never delete the top-level folder wholesale.
- Break down immediate children with exact paths, rank by size, and work one exposed directory at a time.
- Prefer targets whose names imply temporary or regenerable content, such as `临时归档`, installer packages, exported caches, app backup directories, duplicate zip/extracted pairs, and render caches.
- For backup rotation, keep a clear retention policy such as the most recent 30 days.

## Command Patterns

Use these patterns as defaults:

- `df -h /System/Volumes/Data /`
- `du -sh "$HOME/Library/Caches" "$HOME/Downloads" "$HOME/Movies" "$HOME/.Trash" 2>/dev/null`
- `du -sh "$HOME/Downloads"/* 2>/dev/null | sort -hr | head -n 20`
- `du -sh "$HOME/.openclaw" "$HOME/.openclaw"/* 2>/dev/null | sort -hr | head -n 30`
- `mo clean --dry-run --debug`
- `mo installer --dry-run`
- `mo purge --dry-run`
- `openclaw reset --dry-run --scope full --non-interactive --yes`
- `openclaw uninstall --dry-run --workspace --state --service --non-interactive --yes`

## Output Contract

Report:

1. Active mode or the three-option menu.
2. What was inspected.
3. What was deleted, or `preview only`.
4. Space recovered and current free space.
5. The next 1-3 targets, ordered by payoff and risk.
