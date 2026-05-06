# Maintenance Principles

Use these rules whenever cleanup touches Codex, OpenClaw, agent state, sessions, workspaces, logs, caches, or backups.

## Report first

Default to an inventory before changing anything. Report disk pressure, top directories, likely reclaimable space, and risk classification. In mode `1`, stop after the report.

## Backup first

Before changing stateful agent data, create or recommend a backup. This applies to OpenClaw state, Codex state, sessions, workspaces, memory, config, credentials, and plugin directories.

Backup folders can contain private local metadata. Do not publish or share backups without review.

## Archive or rotate before delete

Prefer archive, retention, or rotation when continuity might matter. Examples:

- keep recent backup snapshots and remove older ones
- rotate large logs instead of deleting active log roots
- archive stale workspaces when the user may need handoff context later

Hard delete is acceptable for clearly regenerable caches, trash, temp folders, and explicitly approved user content.

## If running, do not mutate

If the target app, CLI service, gateway, browser profile, or agent process is running, do not mutate its state directories by default.

Allowed while running:

- read-only reports
- dry-run commands
- clearly separate low-risk caches when the app does not depend on them live

Require explicit user approval or a stopped service before changing state, workspace, memory, config, profile, plugin, backup, or session directories.

## Third-party cleanup tools

Treat cleanup tools such as Mole as helpers, not authority. Tool output is evidence, not permission. Review dry-run output and logs before deletion, keep the normal risk tiers, and do not let a tool broaden cleanup scope beyond what the user selected.
