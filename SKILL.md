---
name: mac-cleanup
description: "Use when the user wants macOS disk cleanup: free disk space, clean Mac caches or junk, Downloads triage, Mole-assisted cleanup, or OpenClaw local-state cleanup. Activates on @mac-cleanup, $mac-cleanup, 帮我清Mac垃圾, 清理 Mac 垃圾, 清 OpenClaw 垃圾, 'clean up my mac', 'free up disk space', 'mac is full', 'clear caches', 'disk full', 'reclaim space'. Three modes: 1 dry-run + advice, 2 conservative cleanup, 3 Downloads triage. Ambiguous input shows the menu first; explicit mode wording enters that mode directly. Inspect real machine state, preview before destructive commands, back up stateful agent data, never blind-scan $HOME, and verify with df/du after every change."
---

# Mac Cleanup

Perform conservative macOS cleanup from real machine facts. Inspect first, preview or back up when state matters, delete only low-risk targets automatically, and verify after every mutation.

## Activation

Soft-support these invocations:

- `@mac-cleanup`
- `$mac-cleanup`
- `帮我清Mac垃圾`
- `清理 Mac 垃圾`
- `清 OpenClaw 垃圾`
- `clean up my mac`
- `free up disk space`
- `mac is full`
- `clear caches`
- `reclaim disk space`

For ambiguous activation, the first response must show this menu and stop:

1. `先做 dry-run，再给我删除建议`
2. `直接帮我清理 Mac`
3. `看看 Downloads 还能清什么`

Ambiguous activation includes only naming the skill, `@mac-cleanup`, `$mac-cleanup`, any Chinese trigger, or any English short trigger without a mode.

Skip the menu and enter the mode directly when the user clearly says `1`, `2`, `3`, `dry-run`, `直接帮我清理 Mac`, `直接清理`, `直接删`, `看看 Downloads`, or any equivalent in either language.

When the user asks to clean OpenClaw junk without a mode, enter mode `1` with the OpenClaw rules from [openclaw-cleanup.md](references/openclaw-cleanup.md).

## Modes

1. `dry-run + deletion advice` — Inspect and preview only. Do not delete. Report reclaimable space, low-risk opportunities, and concrete next choices.
2. `direct cleanup` — Inspect first, then delete only low-risk targets automatically. User-owned content, app state, agent state, and uninstall/reset actions require explicit scope.
3. `Downloads triage` — Inspect `~/Downloads` only. Rank immediate children by size and propose narrowly scoped deletes.

## Safety Rules

- Read [maintenance-principles.md](references/maintenance-principles.md) before touching Codex, OpenClaw, app state, workspaces, sessions, logs, or backups.
- Separate risk levels:
  - Low risk: caches, logs, trash, browser cache shards, package-manager caches, orphaned app leftovers, and aged backup folders with a clear retention rule.
  - Explicit approval required: `~/Downloads`, `~/Movies`, installers, app backups, project artifacts, app uninstall, media folders, business documents, OpenClaw non-cache state, Maven repository, Docker / OrbStack daemon-managed data, APFS local snapshots, sleep image, Nix garbage collection.
  - Do not auto-delete: active project directories, business materials, installed apps, credentials, memory, plugins, config, backups, ambiguous folders, swap files in `/private/var/vm`.
- Prefer exact directory coordinates from [dev-bucket-paths.md](references/dev-bucket-paths.md) over discovery sweeps. Do not run blind `find` scans across `$HOME` or large trees.
- If a target tool or app is running, report and preview first. Before mutating its state directories the user must close it or explicitly accept a clearly separated low-risk cache path. Check at least: Xcode, Gradle daemon, Docker / OrbStack, Chrome / browsers, and any named gateway or agent. Probes are in [inspection-commands.md](references/inspection-commands.md).
- Every destructive step must be followed by `df -h` plus targeted `du -sh` verification.

## Inspection Buckets

Walk these in order of payoff and trust. Full path lists, run rules, and clean commands live in [dev-bucket-paths.md](references/dev-bucket-paths.md). Stable read-only commands live in [inspection-commands.md](references/inspection-commands.md).

1. Pressure — `df`, APFS purgeable space, local snapshots, sleep image, swap, hibernate mode.
2. User caches and logs — `~/Library/Caches`, `~/Library/Logs`, `~/Library/Application Support` heavy hitters (Spotify, Slack, Telegram, WeChat, Discord, Dropbox, etc.).
3. Browser leftovers — Chrome, Safari, Firefox, Edge, Brave caches and Service Worker stores. Preserve PWAs the user actually runs.
4. Developer toolchains — Xcode DerivedData / Archives / iOS DeviceSupport / CoreSimulator / Documentation cache, Homebrew, npm / pnpm / yarn / bun / corepack, pip / uv / poetry / conda / pyenv, cargo / rustup, go cache, gem / bundler, gradle, maven, Nix, Docker buildx, OrbStack data, cloud CLIs, ML caches (HF / torch / TF / wandb).
5. System temp — `/private/var/folders/<hash>/<hash>/C` rebuildable caches (stale > 1 day), `/Library/Caches` rotatable items. Sudo required; never auto.
6. Trash and orphans — `~/.Trash`. Report-only on `/Volumes/*/.Trashes` and orphaned app support folders aged past 30 days.
7. User content — `~/Downloads`, `~/Movies`, aged installer / backup folders. Never wholesale.
8. Optional helpers — Mole ([mole-integration.md](references/mole-integration.md)) and OpenClaw ([openclaw-cleanup.md](references/openclaw-cleanup.md)).

## Default Workflow

1. Resolve the mode or show the menu.
2. Run the pressure block from [inspection-commands.md](references/inspection-commands.md).
3. Walk the inspection buckets above. Stop once there is enough evidence to recommend.
4. For mode `1`, stop after preview and report through the Output Contract.
5. For mode `2`, delete only low-risk targets from [dev-bucket-paths.md](references/dev-bucket-paths.md), then verify with `df -h` and targeted `du -sh`.
6. For mode `3`, inspect only `~/Downloads` immediate children and propose concrete deletions.
7. For OpenClaw, apply [openclaw-cleanup.md](references/openclaw-cleanup.md) and prefer `openclaw backup create` plus dry-run reset / uninstall checks before any state-changing action.
8. For Mole, follow [mole-integration.md](references/mole-integration.md).

## User Content

For `~/Downloads`, `~/Movies`, `~/Desktop`, and similar folders:

- Never delete the top-level folder wholesale.
- Break down immediate children with exact paths, rank by size, and work one exposed directory at a time.
- Prefer targets whose names imply temporary or regenerable content: `临时归档`, `*.dmg`, `*.pkg`, installer packages, exported caches, app backup directories, duplicate zip and extracted pairs, render caches, dated archive folders.
- For backup rotation, keep a clear retention policy such as the most recent 30 days.

## Output Contract

Report each run as Markdown with these sections:

1. **Mode** — `1`, `2`, `3`, or the three-option menu.
2. **Pressure** — `df -h` line for the data volume, plus purgeable space and any local snapshot or sleep-image notes.
3. **Inspected** — bullet list of the buckets walked, each with size and risk tier.
4. **Acted** — bullet list of what was deleted with exact paths, or `preview only`.
5. **Recovered** — bytes freed and current free space, or estimated reclaimable space when in mode `1`.
6. **Next** — 1-3 ordered next targets, each with risk tier and the user approval it needs.

When real bytes are not yet known (mode `1`), use `du -sh` as the reclaimable estimate and label it `estimate`.
