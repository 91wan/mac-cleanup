# mac-cleanup

Conservative Codex skill for macOS disk cleanup, Downloads triage, Mole-assisted cleanup, and OpenClaw local-state cleanup.

## Install

Clone the skill into your Codex skills directory:

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
git clone https://github.com/91wan/mac-cleanup "${CODEX_HOME:-$HOME/.codex}/skills/mac-cleanup"
```

Restart Codex if the skill does not appear immediately.

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
- `agents/openai.yaml`
- `references/maintenance-principles.md`
- `references/openclaw-cleanup.md`
- `LICENSE`
