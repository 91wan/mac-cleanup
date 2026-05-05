# mac-cleanup

Conservative Codex skill for macOS disk cleanup, Downloads triage, Mole-assisted cleanup, and OpenClaw local-state cleanup.

## English

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

## OpenClaw Cleanup

OpenClaw cleanup is included as a branch of the same skill. It starts with size inspection and built-in dry runs:

```bash
du -sh "$HOME/.openclaw" "$HOME/.openclaw"/* 2>/dev/null | sort -hr | head -n 30
openclaw reset --dry-run --scope full --non-interactive --yes
openclaw uninstall --dry-run --workspace --state --service --non-interactive --yes
```

The skill recommends `openclaw backup create` before state changes and does not run real reset or uninstall commands unless the user explicitly asks to reset or uninstall OpenClaw.

## Validate

If you have the Codex `skill-creator` skill installed, validate the folder:

```bash
python3 "${CODEX_HOME:-$HOME/.codex}/skills/.system/skill-creator/scripts/quick_validate.py" "${CODEX_HOME:-$HOME/.codex}/skills/mac-cleanup"
```

The repository root is the skill root. Required files are:

- `SKILL.md`
- `AGENTS.md`
- `CLAUDE.md`
- `agents/openai.yaml`
- `references/maintenance-principles.md`
- `references/openclaw-cleanup.md`
- `LICENSE`

## GitHub Release Flow

Before publishing a tag or release, run ShipGate and project validation:

```bash
python3 "$HOME/Skills/shipgate/scripts/shipgate.py" check . --project-type codex-skill --report-md build/shipgate/report.md --report-json build/shipgate/report.json
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
python3 "$HOME/Skills/shipgate/scripts/shipgate.py" check . --project-type codex-skill --asset dist/file.zip --report-md build/shipgate/report.md --report-json build/shipgate/report.json
```

## 中文

## 安装

把 skill 克隆到 Codex skills 目录：

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
git clone https://github.com/91wan/mac-cleanup "${CODEX_HOME:-$HOME/.codex}/skills/mac-cleanup"
```

如果 Codex 没有立即识别这个 skill，重启 Codex。

### Claude Code

把仓库克隆到你存放共享指令的位置，然后让 Claude Code 读取仓库路径或直接读取 `CLAUDE.md`：

```bash
git clone https://github.com/91wan/mac-cleanup "$HOME/Skills/mac-cleanup"
```

Claude Code 不会使用 `agents/openai.yaml`；它应该把 `CLAUDE.md`、`SKILL.md` 和 `references/` 当作 Markdown 指令使用。

### OpenClaw 和通用 agent

克隆仓库后，让 agent 先读取 `AGENTS.md`：

```bash
git clone https://github.com/91wan/mac-cleanup "$HOME/Skills/mac-cleanup"
```

OpenClaw 和通用 agent 应把这个仓库视为 Markdown 指令包。它不是 OpenClaw 原生 plugin 包。

## 使用

可以用 skill 名或自然语言触发：

```text
@mac-cleanup
$mac-cleanup
帮我清Mac垃圾
清理 Mac 垃圾
清 OpenClaw 垃圾
```

模糊触发会先显示三个选项：

1. `先做 dry-run，再给我删除建议`
2. `直接帮我清理 Mac`
3. `看看 Downloads 还能清什么`

明确模式会直接进入对应流程：

```text
用 $mac-cleanup 1
用 $mac-cleanup 2
用 $mac-cleanup 3
直接帮我清理 Mac
看看 Downloads 还能清什么
```

## 支持的项目类型

这个仓库是 `codex-skill` 项目。仓库根目录就是 skill 根目录。

其他 agent 可以读取 Markdown 文件使用，但这个仓库不声明自己是 Claude Code 或 OpenClaw 的原生包格式。

## 安全模型

这个 skill 遵守四个维护门禁：

- 先报告：先检查真实磁盘压力，再做改动。
- 先备份：涉及有状态 agent 数据前，先建议或创建备份。
- 先归档或轮转：连续性可能重要时，优先使用保留规则。
- 运行中不改状态：目标 app、gateway、浏览器 profile、agent 或 workspace 仍在运行时，不直接修改状态目录。

低风险清理只覆盖缓存、日志、废纸篓、包管理器缓存和明确可再生成的临时数据。用户文件、app 状态、项目产物、凭据、memory、config、plugins、backups 和含糊目录都需要明确授权。

## OpenClaw 清理

OpenClaw 清理是同一个 skill 的分支能力。它先做体积检查和内建 dry-run：

```bash
du -sh "$HOME/.openclaw" "$HOME/.openclaw"/* 2>/dev/null | sort -hr | head -n 30
openclaw reset --dry-run --scope full --non-interactive --yes
openclaw uninstall --dry-run --workspace --state --service --non-interactive --yes
```

涉及状态变更前，skill 会建议 `openclaw backup create`。除非用户明确要求重置或卸载 OpenClaw，否则不会运行真正的 reset 或 uninstall。

## 验证

如果本机安装了 Codex 的 `skill-creator` skill，可以验证目录：

```bash
python3 "${CODEX_HOME:-$HOME/.codex}/skills/.system/skill-creator/scripts/quick_validate.py" "${CODEX_HOME:-$HOME/.codex}/skills/mac-cleanup"
```

必需文件：

- `SKILL.md`
- `AGENTS.md`
- `CLAUDE.md`
- `agents/openai.yaml`
- `references/maintenance-principles.md`
- `references/openclaw-cleanup.md`
- `LICENSE`

## GitHub 发布流程

发布 tag 或 release 前，先运行 ShipGate 和项目验证：

```bash
python3 "$HOME/Skills/shipgate/scripts/shipgate.py" check . --project-type codex-skill --report-md build/shipgate/report.md --report-json build/shipgate/report.json
python3 "${CODEX_HOME:-$HOME/.codex}/skills/.system/skill-creator/scripts/quick_validate.py" .
```

然后确认 GitHub 登录和远端：

```bash
gh auth status
git remote -v
```

只有本地验证通过后才 push。如果创建 GitHub release，需要创建 annotated tag、创建 release，并用 ShipGate 报告里的 SHA-256 校验上传资产。

## 资产检查

这个 skill 默认没有二进制发布资产。如果未来 release 包含资产，必须把每个文件显式传给 ShipGate：

```bash
python3 "$HOME/Skills/shipgate/scripts/shipgate.py" check . --project-type codex-skill --asset dist/file.zip --report-md build/shipgate/report.md --report-json build/shipgate/report.json
```
