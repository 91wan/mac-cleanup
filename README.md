# mac-cleanup

English | [中文](README_ZH.md)

Conservative Codex skill for macOS disk cleanup, Downloads triage, Mole-assisted cleanup, and OpenClaw local-state cleanup.

## Install

Clone the skill into your Codex skills directory:

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
git clone https://github.com/91wan/mac-cleanup "${CODEX_HOME:-$HOME/.codex}/skills/mac-cleanup"
```

Restart Codex if the skill does not appear immediately.

### Claude Code

Clone the repository anywhere you keep shared instructions, then point Claude Code at the repository path or ask it to read `CLAUDE.md`:

```bash
git clone https://github.com/91wan/mac-cleanup "$HOME/Skills/mac-cleanup"
```

Claude Code does not use `agents/openai.yaml`; it should treat `CLAUDE.md`, `SKILL.md`, and `references/` as Markdown instructions.

### OpenClaw and generic agents

Clone the repository and instruct the agent to read `AGENTS.md` first:

```bash
git clone https://github.com/91wan/mac-cleanup "$HOME/Skills/mac-cleanup"
```

OpenClaw and generic agents should treat this repository as Markdown guidance. It is not an OpenClaw-native plugin package.

## Use

Invoke the skill by name or with natural language:

```text
@mac-cleanup
$mac-cleanup
帮我清Mac垃圾
清理 Mac 垃圾
清 OpenClaw 垃圾
clean up my mac
free up disk space
mac is full
clear caches
```

Ambiguous activation shows this menu first:

1. `先做 dry-run，再给我删除建议`
2. `直接帮我清理 Mac`
3. `看看 Downloads 还能清什么`

Explicit mode requests go directly to that mode:

```text
用 $mac-cleanup 1
用 $mac-cleanup 2
用 $mac-cleanup 3
直接帮我清理 Mac
看看 Downloads 还能清什么
```

## Supported Project Type

This repository is a `codex-skill` project. The repository root is the skill root.

Other agents can consume the Markdown files, but the repository does not claim native Claude Code or OpenClaw package format support.

## Safety Model

The skill follows four maintenance gates:

- Report first: inspect real disk pressure before changing anything.
- Backup first: recommend or create backups before touching stateful agent data.
- Archive or rotate before delete: prefer retention rules where continuity may matter.
- If running, do not mutate: avoid changing live app, gateway, browser-profile, agent, or workspace state.

Low-risk cleanup is limited to caches, logs, trash, package-manager caches, and clearly regenerable temporary data. User files, app state, project artifacts, credentials, memory, config, plugins, backups, and ambiguous folders require explicit scope.

The per-ecosystem cleanup catalogue lives in [references/dev-bucket-paths.md](references/dev-bucket-paths.md) and covers Xcode, Homebrew, npm/pnpm/yarn/bun, pip/uv/poetry/conda, cargo/rustup, Go, gem/bundler, Maven, Gradle, Android, Nix, Docker BuildX / OrbStack, browsers, heavy native apps (Spotify, Slack, Telegram, WeChat, Discord), system temp, and APFS state (local snapshots, sleep image). Inspection commands live in [references/inspection-commands.md](references/inspection-commands.md).

## OpenClaw Cleanup

OpenClaw cleanup is included as a branch of the same skill. It starts with size inspection and built-in dry runs:

```bash
du -sh "$HOME/.openclaw" "$HOME/.openclaw"/* 2>/dev/null | sort -hr | head -n 30
openclaw reset --dry-run --scope full --non-interactive --yes
openclaw uninstall --dry-run --workspace --state --service --non-interactive --yes
```

The skill recommends `openclaw backup create` before state changes and does not run real reset or uninstall commands unless the user explicitly asks to reset or uninstall OpenClaw.

## Mole-assisted Cleanup

Mole support is optional. The skill does not install Mole automatically and does not vendor Mole scripts.

When Mole is available, the skill uses preview commands first:

```bash
command -v mo
mo --version
mo clean --dry-run --debug
mo installer --dry-run
mo purge --dry-run
```

`mo clean` is the normal low-risk Mole path after preview. `mo installer`, `mo purge`, `mo optimize`, `mo uninstall`, `mo remove`, `mo touchid`, and `mo completion` require explicit approval.

## Validate

If you have the Codex `skill-creator` skill installed, validate the folder:

```bash
python3 "${CODEX_HOME:-$HOME/.codex}/skills/.system/skill-creator/scripts/quick_validate.py" "${CODEX_HOME:-$HOME/.codex}/skills/mac-cleanup"
```

The repository root is the skill root. Required files are:

- `SKILL.md`
- `README.md`
- `README_ZH.md`
- `AGENTS.md`
- `CLAUDE.md`
- `agents/openai.yaml`
- `references/inspection-commands.md`
- `references/dev-bucket-paths.md`
- `references/maintenance-principles.md`
- `references/openclaw-cleanup.md`
- `references/mole-integration.md`
- `LICENSE`

## GitHub Release Flow

Before publishing a tag or release, run ShipGate and project validation:

```bash
python3 "${CODEX_HOME:-$HOME/.codex}/skills/shipgate/scripts/shipgate.py" check . --project-type codex-skill --report-md build/shipgate/report.md --report-json build/shipgate/report.json
python3 "${CODEX_HOME:-$HOME/.codex}/skills/.system/skill-creator/scripts/quick_validate.py" .
```

Then confirm GitHub auth and remote:

```bash
gh auth status
git remote -v
```

Push only after local validation passes. If creating a GitHub release, create an annotated tag, create the release, and verify any uploaded release assets against the ShipGate SHA-256 report.

## Asset Verification

This skill has no binary release asset by default. If a future release includes assets, pass each file explicitly to ShipGate:

```bash
python3 "${CODEX_HOME:-$HOME/.codex}/skills/shipgate/scripts/shipgate.py" check . --project-type codex-skill --asset dist/file.zip --report-md build/shipgate/report.md --report-json build/shipgate/report.json
```

## 中文覆盖说明

中文完整文档在 [README_ZH.md](README_ZH.md)。本段保留给当前 ShipGate 检查器识别中文发布覆盖：安装、使用、项目、验证、GitHub 发布、资产检查。
