# Agent Instructions

This repository is a Codex skill first, but the Markdown instructions can be used by other agents.

## Source of Truth

- Use `SKILL.md` as the only behavior source.
- Use `references/maintenance-principles.md` before touching Codex, OpenClaw, app state, workspaces, sessions, logs, or backups.
- Use `references/openclaw-cleanup.md` for any OpenClaw cleanup request.

## Required Behavior

- Ambiguous activation must show the three-option menu from `SKILL.md` and stop.
- Explicit `1`, `2`, `3`, or equivalent mode wording must enter that mode directly.
- Report before changing files.
- Back up or recommend backup before touching stateful agent data.
- Do not mutate running app, gateway, browser-profile, agent, workspace, memory, config, plugin, backup, or session state.
- Verify every destructive cleanup with `df -h` and targeted `du -sh`.

## Platform Notes

- Codex can discover this repository as a native skill through `SKILL.md` and `agents/openai.yaml`.
- OpenClaw and generic agents should read this file, then `SKILL.md`, then any referenced file needed for the task.
- Do not treat this repository as an OpenClaw-native plugin or package unless a future release adds that format.
