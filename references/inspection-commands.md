# Inspection Commands

Stable read-only commands. Run these before any destructive step. Prefer exact paths; never blind-scan `$HOME` or `/`.

## Pressure

- Data volume usage: `df -h /System/Volumes/Data /`
- Free + container free on the boot APFS volume: `diskutil info / | grep -E "Container Free Space|Available Space"`
- APFS local Time Machine snapshots (Big Sur+): `tmutil listlocalsnapshots /`
- Sleep image and swap files: `ls -lh /private/var/vm/sleepimage /private/var/vm/swapfile* 2>/dev/null; sysctl vm.swapusage`
- Hibernate mode: `pmset -g | grep hibernatemode`
- macOS purge hint (free up purgeable on APFS): `sudo /usr/sbin/purge` — surface only as a suggestion; never auto.

## Top-level user buckets

```bash
du -sh \
  "$HOME/Library/Caches" \
  "$HOME/Library/Logs" \
  "$HOME/Library/Application Support" \
  "$HOME/Library/Containers" \
  "$HOME/Downloads" \
  "$HOME/Movies" \
  "$HOME/Desktop" \
  "$HOME/.Trash" \
  2>/dev/null
```

## Top-N inside a bucket

- `du -sh "$HOME/Library/Caches"/* 2>/dev/null | sort -hr | head -n 20`
- `du -sh "$HOME/Library/Application Support"/* 2>/dev/null | sort -hr | head -n 20`
- `du -sh "$HOME/Library/Containers"/* 2>/dev/null | sort -hr | head -n 20`
- `du -sh "$HOME/Downloads"/* 2>/dev/null | sort -hr | head -n 20`

## Developer hot spots

```bash
du -sh \
  "$HOME/Library/Developer/Xcode/DerivedData" \
  "$HOME/Library/Developer/Xcode/iOS DeviceSupport" \
  "$HOME/Library/Developer/Xcode/Archives" \
  "$HOME/Library/Developer/CoreSimulator/Devices" \
  "$HOME/Library/Developer/CoreSimulator/Caches" \
  2>/dev/null
```

```bash
du -sh \
  "$HOME/Library/Caches/Homebrew" \
  "$HOME/.npm" \
  "$HOME/Library/pnpm" \
  "$HOME/.yarn" \
  "$HOME/.bun" \
  "$HOME/.cache" \
  "$HOME/.cargo" \
  "$HOME/.rustup" \
  "$HOME/go" \
  "$HOME/.m2/repository" \
  "$HOME/.gradle" \
  "$HOME/Library/Android/sdk" \
  2>/dev/null
```

```bash
du -sh \
  "$HOME/Library/Containers/com.docker.docker" \
  "$HOME/.docker" \
  "$HOME/Library/Group Containers"/*dev.orbstack/data \
  2>/dev/null
```

## Browsers

```bash
du -sh \
  "$HOME/Library/Caches/Google/Chrome" \
  "$HOME/Library/Caches/com.apple.Safari" \
  "$HOME/Library/Caches/com.brave.Browser" \
  "$HOME/Library/Caches/com.microsoft.edgemac" \
  "$HOME/Library/Caches/Firefox" \
  2>/dev/null
```

## Heavy native-app caches

```bash
du -sh \
  "$HOME/Library/Caches/com.spotify.client" \
  "$HOME/Library/Caches/com.tinyspeck.slackmacgap" \
  "$HOME/Library/Caches/ru.keepcoder.Telegram" \
  "$HOME/Library/Caches/com.hnc.Discord" \
  "$HOME/Library/Containers/com.tencent.xinWeChat/Data/Library/Caches" \
  2>/dev/null
```

## OpenClaw

- `du -sh "$HOME/.openclaw" "$HOME/.openclaw"/* 2>/dev/null | sort -hr | head -n 30`
- `du -sh "$HOME/.openclaw/workspace"/* 2>/dev/null | sort -hr | head -n 30`

## Process probes before mutating state

- Xcode: `pgrep -x Xcode >/dev/null && echo running || echo idle`
- Gradle daemon: `pgrep -f 'org.gradle.launcher.daemon|GradleDaemon' >/dev/null && echo running || echo idle`
- Docker / OrbStack: `pgrep -f 'com.docker.backend|Docker Desktop|OrbStack' >/dev/null && echo running || echo idle`
- Chrome / Safari / Firefox: `pgrep -x 'Google Chrome' 'Safari' 'firefox' 2>/dev/null`
- Node tooling holding caches: `pgrep -f 'node|pnpm|yarn|bun' 2>/dev/null`

## Verification after destructive steps

- `df -h /System/Volumes/Data /`
- `du -sh <changed_paths>`
- For Trash: `ls -A "$HOME/.Trash" | wc -l`

## Hardening rules

- Always cap recursion. Prefer `du -sh <exact path>` over `find $HOME`. Use `-maxdepth 1` on Top-N listings.
- Quote every path; append `2>/dev/null` to skip permission noise.
- For SIP-protected or sudo-only paths (`/Library/Caches`, `/private/var/folders`, `/private/var/vm`), surface size but do not auto-mutate without explicit user approval.
- `df` and `du` measure differently on APFS — APFS holds **purgeable** space that the OS releases under pressure. Note this in the report when free space looks lower than `du` would imply.
- Set `LC_ALL=C` when parsing `sort -hr` to keep ordering deterministic across locales.
- Cap any directory walk that could span the whole disk; if a probe runs longer than ~10s, abort and switch to a narrower path.
