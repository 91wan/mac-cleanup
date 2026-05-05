# Claude Code Instructions

Use this repository as a Markdown instruction pack for Mac cleanup work.

## How To Use

1. Read `SKILL.md`.
2. Follow the activation and mode rules exactly.
3. Read `references/maintenance-principles.md` before touching stateful agent data.
4. Read `references/openclaw-cleanup.md` for OpenClaw cleanup requests.

## Important Boundaries

- `agents/openai.yaml` is Codex/OpenAI metadata and does not make this a Claude-native skill.
- Do not copy the full skill text into another file. Keep `SKILL.md` as the behavior source.
- Do not run destructive cleanup until the selected mode allows it.
- For ambiguous requests such as `帮我清Mac垃圾`, show the three-option menu from `SKILL.md` first.
- For OpenClaw state, prefer dry-run and backup before changing anything.
