# Mole Integration

Use this reference only when the user mentions Mole, `mo`, or asks for Mole-assisted cleanup. Mole is an optional helper, not a required dependency and not the source of cleanup authority.

When Mole is unavailable, address the same buckets directly using [dev-bucket-paths.md](dev-bucket-paths.md). The skill's coverage is independent of Mole; Mole is a convenience layer that bundles preview, whitelisting, and operation logs.

## Detection

Check local availability before using Mole:

- `command -v mo`
- `mo --version`
- `mo --help`

If `mo` is not available, do not install it automatically. Report that Mole is unavailable and continue with the normal `mac-cleanup` workflow unless the user explicitly asks to install Mole.

## Preview Commands

Use preview output as evidence before any real cleanup:

- `mo clean --dry-run --debug`
- `mo installer --dry-run`
- `mo purge --dry-run`
- `mo optimize --dry-run`
- `mo uninstall --dry-run`

For mode `1`, stop after preview and advice. For mode `2`, preview first, then continue only with low-risk cleanup unless the user explicitly approves a higher-risk command.

## Risk Tiers

Normal low-risk path after preview:

- `mo clean`

Explicit approval required:

- `mo installer`
- `mo purge`
- `mo optimize`
- `mo uninstall`
- `mo remove`
- `mo touchid`
- `mo completion`

Do not use `mo uninstall`, `mo remove`, `mo purge`, or `mo optimize` just because they appear to reclaim more space. Prefer `mo clean` when cache cleanup solves the pressure.

## Safety Boundaries

Mole's safety model emphasizes destructive command boundaries, protected directories, path validation, sudo boundaries, symlink/path traversal handling, credential exclusions, and release integrity. Apply those ideas as operating constraints:

- Treat tool output as evidence, not permission.
- Do not broaden deletion scope when Mole refuses, skips, or is uncertain.
- Do not delete user documents, credentials, active project directories, app state, memory, config, plugins, backups, or ambiguous folders unless the user explicitly scoped that target.
- Prefer exact paths and selected targets over broad scans.
- Prefer `mo analyze` for visual triage when the user wants to inspect large folders. For ad hoc file removal, prefer Trash/Finder-style removal over direct hard delete when possible.
- If an app, browser profile, project, gateway, or agent appears active, preview only unless the selected mode and user approval allow a clearly separate low-risk cache cleanup.

## Review Contract

When reporting Mole work, include:

1. Exact Mole command used.
2. Whether it was preview-only or destructive.
3. Risk tier.
4. Reclaimable or recovered space.
5. Notable skipped or protected paths.
6. `~/Library/Logs/mole/operations.log` when a real Mole cleanup ran and log review is useful.
