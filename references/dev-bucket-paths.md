# Developer and App Bucket Paths

Catalogue of macOS cleanup buckets keyed by ecosystem. Use exact paths; never glob across `$HOME`. Distilled from real-world Mac cleanup tools (including [tw93/Mole](https://github.com/tw93/Mole)) without vendoring their code.

Risk tiers:

- **L (low)**: auto-delete in mode `2` after preview. Caches that fully regenerate.
- **M (medium)**: preview only unless the user names the target. Often large but slow to repopulate.
- **H (high)**: never delete by default. State, credentials, installed software.

## Xcode and iOS

- L `~/Library/Developer/Xcode/DerivedData/*` — only when `pgrep -x Xcode` is idle. Each subdirectory is one project build; safe to remove.
- L `~/Library/Developer/Xcode/Archives/<date>/*` older than the user's retention.
- M `~/Library/Developer/Xcode/iOS DeviceSupport/*` — keep versions matching attached devices.
- M `~/Library/Developer/Xcode/watchOS DeviceSupport/*`, `tvOS DeviceSupport/*`.
- M `~/Library/Developer/CoreSimulator/Devices/*` — prefer `xcrun simctl delete unavailable` first.
- M `~/Library/Developer/CoreSimulator/Caches/*`
- M `/Library/Developer/Xcode/DocumentationCache/*` — keep the most recent index, prune older.
- H Installed Xcode app, profiles, certificates, signing keys.

Helpful commands:

- `xcrun simctl delete unavailable`
- `xcrun simctl shutdown all`

## Homebrew

- L `brew cleanup -s --prune=all`
- L `~/Library/Caches/Homebrew/downloads/*`
- L `~/Library/Caches/Homebrew/api/*`
- M `brew autoremove` — surface plan first, confirm before running.

## Node, JavaScript, TypeScript

- L `npm cache clean --force` and orphans in `~/.npm/{_cacache,_npx,_logs,_prebuilds}/*`
- L `pnpm store prune` (fallback `~/Library/pnpm/store/*` only if pnpm absent and user opts in)
- L `bun pm cache rm` and `~/.bun/install/cache/*`
- L `~/.yarn/cache/*`, `~/Library/Caches/Yarn/*`
- L `~/.cache/node/corepack/*`
- L `~/.cache/typescript/*`, `~/.cache/electron/*`, `~/.cache/node-gyp/*`, `~/.node-gyp/*`
- L `~/.turbo/cache/*`, `~/.vite/cache/*`, `~/.cache/vite/*`, `~/.cache/webpack/*`, `~/.parcel-cache/*`
- L `~/.cache/eslint/*`, `~/.cache/prettier/*`
- L `~/.tnpm/_cacache/*`, `~/.tnpm/_logs/*`
- M `node_modules` directories — only inside an explicit purge scope or with user-named paths.

## Python and ML

- L `pip3 cache purge` and `~/Library/Caches/pip/*`
- L `uv cache prune` and `~/.cache/uv/*`
- L `~/.cache/poetry/*`, `~/.pyenv/cache/*`
- L `~/.cache/ruff/*`, `~/.cache/mypy/*`, `~/.pytest_cache/*`
- L `~/.jupyter/runtime/*`
- M `~/.cache/huggingface/*`, `~/.cache/torch/*`, `~/.cache/tensorflow/*`, `~/.cache/wandb/*` — large model caches; preview first because re-downloads are expensive.
- M `conda clean --yes --index-cache --tarballs --logfiles` — only when conda is installed; never auto-delete `~/{anaconda3,miniconda3,miniforge3,mambaforge}/pkgs` without conda.

## Rust, Go, Ruby, JVM, Android

- L `~/.cargo/registry/cache/*`, `~/.cargo/git/*`, `~/.rustup/downloads/*`
- L `go clean -cache && go clean -modcache` (resolves `$GOCACHE` and `$GOMODCACHE`).
- L `~/.rbenv/cache/*`, `~/.gem/specs/*`, `~/.gem/ruby/*/cache/*.gem`, `~/.bundle/cache/*`
- L `~/.cpan/build/*`, `~/.cpan/sources/*`
- M `~/.m2/repository/*` — Maven local. Only with explicit user approval; downloads rebuild on next build.
- M `~/.gradle/caches/*` — only when Gradle daemon is idle.
- M `~/Library/Android/sdk/ndk/*` — keep current NDK; remove older versions explicitly.

## Containers and cloud CLIs

- L `~/.docker/buildx/cache/*`
- L `~/.kube/cache/*`, `~/.aws/cli/cache/*`, `~/.config/gcloud/logs/*`, `~/.azure/logs/*`
- L `~/.local/share/containers/storage/tmp/*`
- M `docker system df`; `docker system prune --filter until=720h` — never auto, daemon-managed.
- M OrbStack: `~/Library/Group Containers/*dev.orbstack/data` — surface size only; daemon-managed.
- H Container volumes that hold project data.

## Nix

- M `nix-collect-garbage --delete-older-than 30d` — only with explicit user approval.

## Browsers

- L `~/Library/Caches/com.apple.Safari/*`
- L `~/Library/Caches/Google/Chrome/Default/Cache/*`
- L `~/Library/Caches/Google/Chrome/Default/Code Cache/*`
- L `~/Library/Caches/Google/Chrome/Default/GPUCache/*`
- L `~/Library/Caches/com.brave.Browser/*`, `~/Library/Caches/com.microsoft.edgemac/*`, `~/Library/Caches/Firefox/*`
- M `~/Library/Caches/Google/Chrome/Default/Service Worker/*` — preserve PWAs the user runs (Figma, Notion, Linear, Slack web); preview first.
- H `~/Library/Application Support/Google/Chrome/Default/{Bookmarks,History,Cookies,Login Data}`, equivalent paths for other browsers.

## Heavy native-app caches

- L `~/Library/Caches/com.spotify.client/*`
- L `~/Library/Caches/com.tinyspeck.slackmacgap/*`
- L `~/Library/Caches/ru.keepcoder.Telegram/*`
- L `~/Library/Caches/com.hnc.Discord/*`
- L `~/Library/Caches/com.tencent.meeting/*`
- L `~/Library/Caches/com.dropbox.DropboxMacUpdate/*`
- M `~/Library/Containers/com.tencent.xinWeChat/Data/Library/Caches/*` — WeChat cache shards.
- M `~/Library/Containers/com.tencent.xinWeChat/Data/Library/Application Support/.../FileStorage/*` — received files; large but user-visible.
- H `~/Library/Containers/com.tencent.xinWeChat/Data/Library/Application Support/.../Message/*` — WeChat chat history.
- H `~/Library/Containers/com.tencent.qq/Data/Library/Application Support/*` — QQ state and chats.

## System temp and rotatable (sudo)

- L `~/.Trash/*` (only the user home Trash; never `/Volumes/*/.Trashes` automatically — that is per-volume Trash and may belong to other users).
- M `/Library/Caches/*` — sudo required; prefer rotation rules (>14d, >`*.cache`, >`*.tmp`, >`*.log`) over wholesale delete.
- M `/private/var/folders/<hash>/<hash>/C/com.apple.metal/*`, `com.apple.gpuarchiver/*`, `com.apple.metalfe/*` — stale-only (no contained file modified within 1 day).
- M `/Library/Logs/DiagnosticReports/*` older than retention.
- M Quick Look thumbnails: `qlmanage -r cache` (regenerates on demand).
- H `/private/var/db`, `/private/var/log` system data, `/Library/Application Support` system items.

## APFS and macOS power state

- M Time Machine local snapshots: `tmutil deletelocalsnapshots <date>` — only with explicit user approval and dates from `tmutil listlocalsnapshots /`.
- M Sleep image: `sudo rm /private/var/vm/sleepimage` — only when laptop hibernate is unused and user approves. macOS will recreate it on the next sleep.
- H Swap files in `/private/var/vm/swapfile*` — never touch; macOS manages them and may panic the system.
- M `sudo /usr/sbin/purge` — releases APFS purgeable space; preview as suggestion, never auto.

## User content (`~/Downloads`, `~/Movies`, `~/Desktop`)

- Never delete the top-level folder.
- Rank immediate children with `du -sh "$DIR"/* 2>/dev/null | sort -hr | head -n 20`.
- Acceptable targets when the user explicitly approves the scope: `*.dmg`, `*.pkg`, `*-installer-*`, dated archive folders (`*-2024-*`), duplicate `*.zip` next to its extracted folder, exported render caches, browser-saved video buffers.
- High risk: any folder whose name implies projects, source code, business documents, or media originals.

## Mole-managed paths

When Mole (`mo`) is available, prefer `mo clean --dry-run --debug` to enumerate live targets — Mole keeps its own whitelist and timeout machinery. See [mole-integration.md](mole-integration.md) for tier rules. When Mole is absent, this catalogue covers the same buckets directly.
